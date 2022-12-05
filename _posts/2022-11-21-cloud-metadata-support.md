---
layout: single
title:  "Cloud Metadata Support"
date:   2022-11-21 12:01:25 +0100
tags:
- 0.5.0
- cloud
- Kubernetes
- OpenShift
author_profile: true
author: Axel Tanner
---

Today's workloads are increasingly moving to cloud environments, while at the same time becoming more interconnected and complex.
Typical applications now consist of a collection of different containers/pods that have to play together in a dynamically orchestrated
composition. Monitoring and understanding what is going on in such an environment entails not only seeing the low-level events per container,
but also knowing the role that the container plays in the larger context of a cluster.

[SysFlow 0.5.0](https://sysflow.io/2022/10/19/sysflow-0.5.0.html) introduces support to gather additional metadata for containers in Kubernetes and OpenShift
environments, such as their connection to pods, their places in namespaces, as well as cloud-level events, to enable this additional level of insight.

This is demonstrated in our recent [Jupyter notebook _SysFlow support for Kubernetes environments_](https://nbviewer.org/github/sysflow-telemetry/sf-apis/blob/master/pynb/notebooks/K8sDemo/k8sDemo.ipynb).
In this notebook, we use data captured from monitoring a sample microservice application, [Instana's robot shop](https://github.com/instana/robot-shop)
being deployed for testing purposes in a [`minikube`](https://kubernetes.io/docs/tutorials/hello-minikube/) environment.
Using the latest SysFlow version, which captures new cloud metadata, we find and demonstrate what we can learn about the different containers
that are part of the application and the life-cycle events we are able to gather in that Kubernetes environment.

This is an initial demo of what can be done with the additional data &mdash; have a look, test it yourself and let us know any ideas you might have to
use this data more deeply &mdash; or where there might be areas that should be added!
