Welcome to SysFlow Telemetry Pipeline's documentation!
======================================================

Introduction
============

The SysFlow Telemetry Pipeline is a fully integrated framework for monitoring cloud workloads and for creating performance and security analytics. The goal of the work is to build all the plumbing required for system telemetry, so that users can focus on writing and sharing analytics on a scalable common open source platform. The backbone of the telemetry pipeline is a new data format called SysFlow which lifts raw system event information into an abstraction that describes process behaviors, and their relationships with containers, files, and network. This object relational format is highly compact but provides broad visibility into container clouds. We have also built several APIs that allow users to process SysFlow with their favorite toolkits.

The pipeline can currently be deployed using Docker or Kubernetes through helm charts (see sf-deployments project). We hope to add other deployment options such as OpenShift in the future.

Lastly, there are C++ and python apis available in the sf-apis project that allow users to interact with SysFlow Files directly. There are also Apache Avro schema files for SysFlow so that users can generate API's for any number of other languages such as golang or JAVA.

To learn more about each project, please see the table of contents below, or visit the README's in each project's git repo.

> License: SysFlow and all projects are released under the Apache v2.0 license.

.. toctree::
   :maxdepth: 2
   :caption: Contents:
  
   collector
   exporter
   api-utils
   deploy
   license
   contributing
   coc 

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
