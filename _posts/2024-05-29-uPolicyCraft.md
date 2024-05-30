---
layout: single
title:  "uPolicyCraft - synthesizing and monitoring effect graph policies on SysFlow telemetry"
date:   2024-05-29 15:00:00 -07:00
tags:
- stateful system call specialization
- binary analysis
- sysflow
- container telemetry
- microservices
author_profile: false
author: William Blair
---

<img align="center" src="https://github.com/sysflow-telemetry/upolicycraft/assets/928389/c053254f-b379-446a-a74a-c6569a18a5d7" width="80" height="80">

Today we are excited to share [uPolicyCraft](https://github.com/sysflow-telemetry/upolicycraft), a system for synthesizing effect graph policies that are enforced with microservice-aware policy monitors (MPMs) built on top of the SysFlow telemetry pipeline. uPolicyCraft takes advantage of the rich contexts available in microservice container images to produce stateful security policies tailored to a specific configuration of executables located within a microservice. This has the advantage of restricting a microservice' side-effects, represented as system calls and arguments, to those associated with a specific configuration. For example, most web servers such as `NGINX` or Apache contain numerous feature to accommodate a variety of use cases. In practice, most microservices will typically use a subset of the server's full functionality (e.g., a proxy server for a set of application servers). To reduce a microservice' attack surface, we synthesize stateful security policies, called effect graphs, using a microservice' binary entrypoint and layered filesystem image. These effect graphs represent security automata that a microservice-ware policy monitor (MPM) can use to detect policy violations over SysFlow's lightweight container telemetry.

In this post, we provide an overview of uPolicyCraft, and present an end-to-end example of using uPolicyCraft and SysFlow to protect a proxy web server. We present an attack scenario detected by our SysFlow MPM, and discuss the possibility of mimicry attacks. Further details on uPolicyCraft can be found on the project's [github](https://github.com/sysflow-telemetry/upolicycraft) and in our [paper](https://research.ibm.com/publications/automated-synthesis-of-effect-graph-policies-for-microservice-aware-stateful-system-call-specialization) which appeared at the 2024 IEEE Symposium on Security and Privacy.

## Effect Graph Synthesis

Micro-executing a container entrypoint within the [Binary Analysis Platform](https://github.com/BinaryAnalysisPlatform/bap) (BAP) allows introspection into a binary's lifted intermediate representation (IR) while micro-executing the binary on concrete inputs within Primus. This allows uPolicyCraft to record the ID for each IR term that incurs a side-effect (i.e., issues a system call), and recover the concrete arguments passed to each system call. An effect graph is constructed by creating an edge (u, v) between the last visited effectful term u, and the current term v. Th edge (u, v) is labeled by the system call s that occurs at v, and v is annotated with the concrete arguments passed to s. At the beginning of micro execution, a microservice' effect graph is initialized with a node to denote the original `EXEC` that executes the binary.

The following effect graph is generated for the `NGINX` web server configured to act as a proxy server for one or more microservice applications. Micro-executing an `NGINX` binary with test inputs produces an effect graph, part of which is visualized below. Note that this effect graph can be represented as a security automaton for detecting policy violations within an `NGINX` microservice. That is, if an adversary were to exploit a bug in `NGINX` that allows them to implement a server-side request forgery (SSRF) attack, the exploit's `CONNECT` deviates from the strongly connected component given below within the `NGINX` effect graph.

<a href="/assets/images/nginx-attack.svg"><img src="/assets/images/nginx-attack.svg"/></a>

## Microservice-Aware Policy Monitoring

We can pass this security automaton directly to the Microservice-Aware Policy Monitor (MPM) which is implemented as a SysFlow plugin. Intuitively, you can think of uPolicyCraft as approximating a type inference procedure to determine the "type" for a given microservice. In this setting, types encode valid system call sequences and arguments individual microservices may issue, similar to how types in programming languages denote the kind of values that may be assigned to individual terms. In this setting, you can think of effect graphs as types within a dependently typed language for UNIX programs (i.e., the type for a microservice is allowed to contain system call arguments, similar to dependent types refer to individual program values). In this view, the MPM's goal is to "typecheck" container telemetry represented as a stream of SysFlow records.

Since records may appear out of order, we record the operations and resources that appear in a trace (i.e., an open on a configuration file "/etc/nginx/nginx.conf"), and use these primitives to advance the microservice' automaton, represented as an effect graph. When an operation forbidden by the security automaton appears in the SysFlow telemetry, the MPM observes the operation cannot use the operation to advance the automaton, and an alert is raised to the operator. In our `NGINX` example, the `CONNECT` that appears while attempting to reach out to a command and control server causes the microservice' automaton to become stuck once the server starts handling connections, which causes the MPM to immediately raise an alert. Note that this violation can allow an investigator to follow the full exploit chain within SysFlow telemetry in order to determine the full impact of a policy violation, including whether the adversary installs an advanced persistent threat (APT) and/or impacts other co-located microservices.

In this example, we showed how an MPM can protect an individual microservice. The MPM is implemented to apply multiple effect graphs across a SysFlow telemetry stream, which can be produced by many different microservices running on a physical node. This enforces the "distributed effect graph", which is the composition of individual effect graphs, for monitoring whole microservice architectures.

## Mimicry Attacks

It is important to note that mimicry attacks may be possible on effect graph policies. Mimicry attacks conform to a given security policy, but achieve some malicious goal. For example, in the effect graph given for the `NGINX` webserver, an adversary could disclose the contents of process memory by writing sensitive data back out to the original socket accepted on port 443. The most effective defense against these kind of mimicry attacks is to employ control-flow integrity (CFI) to limit the adversary's ability to subvert the control flow of an application. Furthermore, observe that an adversary can only achieve this mimicry attack by writing back out to a socket that is accepted by the server. If an adversary attempts to bind to another port, duplicate a socket file descriptor (e.g., via `dup` or `dup2`), or connect to any host forbidden by the effect graph, then the MPM will raise a violation for the SysFlow telemetry stream.
