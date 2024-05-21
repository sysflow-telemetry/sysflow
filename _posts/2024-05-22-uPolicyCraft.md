---
layout: single
title:  "uPolicyCraft - synthesizing and monitoring effect graph policies on SysFlow telemetry"
date:   2024-05-22 15:00:00 -07:00
tags:
- system call specialization
- binary analysis
- sysflow
author_profile: false
author: William Blair
---

Today we are excited to share [uPolicyCraft](https://github.com/sysflow-telemetry/upolicycraft), a system for synthesizing effect graph policies and enforced with microservice-aware policy monitors (MPMs) built on top of the SysFlow telemetry pipeline. uPolicyCraft takes advantage of the rich contexts available in container images to produce security policies tailored to a specific configuraton of a binary executable located within a microservice. This has the advantage of restricting a microservice' side-effects, represented as system calls and arguments, to those incurred for a specific configuration. For example, most web servers such as NGINX or Apache contain numerous feature to accomodate a variety of use cases. In practice, most microservices will typically use a subset of the server's full functionality (e.g., as a proxy server for an application). To reduce a microservice' attack surface, we synthesize stateful security policies, called effect graphs, using a microservice' binary entrypoint and layered filesystem image. These effect graphs represent security automata that a microservice-ware policy monitor (MPM) can enforce over SysFlow's lightweight container telemetry.

In this post, we provide an overview of uPolicyCraft, and present a simple end-to-end example of using uPolicyCraft and SysFlow to protect a simple network server. We present an attack scenario detected by our Sysflow MPM, and discuss the possibility of mimicry attacks. Further details on uPolicyCraft can be found on the project's [github](https://github.com/sysflow-telemetry/upolicycraft) and in our [paper](https://github.com/sysflow-telemetry/upolicycraft) which appeared at the 2024 IEEE Symposium on Security and Privacy

## Effect Graph Synthesis

Micro-executing a container entrypoint within the [Binary Analysis Platform](https://github.com/BinaryAnalysisPlatform/bap) (BAP) allows introspection into a binary's lifted representation (IR) while executing the binary on concrete inputs within Primus. This allows uPolicyCraft to record the ID for each IR term that incurs a side-effect (i.e., issues a system call), and recover the concrete arguments passed to each system call. An effect graph is constructed by creating an edge (u, v) between the last visited effectful term u, and the current term v. Th edge (u, v) is labeled by the system call s that occurs at v, and v is annotated with the concrete arguments passed to s. At the beginning of micro-execution, the effect graph is initialized with an initial node to denote the original `EXEC` for the binary.

The following snippet is from an `echo` web server that simply sends any data it receives back to a remote client. Micro-executing an `echo` binary with test inputs produces the following effect graph. Note that the effect graph can be represented as a security automaton for detecting policy violations for a microservice running `echo`. That is, if an adversary were to exploit a buffer overflow that allows them to jump to an arbitrary address and spawn a reverse shell, the shell's `EXEC` would deviate from the loop apparent in `echo`'s effect graph.

## Microservice-Aware Policy Monitoring

We can pass this security automaton directly to the Microservice-Aware Policy Monitor (MPM) which is implemented as a SysFlow plugin. Intuitively, you can think of uPolicyCraft as approximating a type inference procedure to determine the "type" for a given microservice. In this setting, types encode valid system call sequences and arguments individual microservices may issue, similar to how types in programming languages denote the kind of values that may be assigned to individual terms. In this setting, you can think of effect graphs as a dependently typed language for UNIX system calls (i.e., the type for a microservice is allowed to contain system call arguments, like how dependent types can contain individual program values). In this view, the MPM's goal is to "typecheck" container telemetry represented as a stream of SysFlow records. Since records may appear out of order, we record the operations and resources that appear in a trace (i.e.,an open on a configuration file "/echo.conf"), and use these primitives to advance the microservice' automaton. When an operation forbidden by the security automaton appears in the Sysflow telemetry, the MPM observes the operation cannot use the operation to advance the automaton, and an alert is raised to the operator. In our `echo` example, the `EXEC` that appears after opening a reverse shell cannot advance the microservice' automaton once the server starts handling connections, which causes an alert to be immediately raised. Note that this violation can allow an investigator to follow the full exploit chain within Sysflow telemetry in order to determine the full impact of a policy violation, including whether the adversary installs an advanced persistent threat (APT) and/or impacts other microservices.

In this example, we showed how an MPM can protect an individual microservices, but the MPM is implemented to apply multiple effect graphs across a Sysflow telemetry stream. This enforces the "distributed effect graph" for monitoring whole microservice architectures.

&ndash; William Blair &#9829;
