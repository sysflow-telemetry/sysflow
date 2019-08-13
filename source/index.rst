.. SysFlow Telemetry Pipeline documentation master file, created by
   sphinx-quickstart on Wed Aug  7 16:34:09 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to SysFlow Telemetry Pipeline's documentation!
======================================================

Introduction
============

The SysFlow Telemetry Pipeline is a fully integrated framework for monitoring cloud workloads and for creating performance and security analytics. The goal of the work is to build all the plumbing required for system telemetry, so that users can focus on writing and sharing analytics on a scalable common open source platform.  The backbone of the telemetry pipeline is a new data format called SysFlow which lifts raw system event information into an abstraction that describes process behaviors, and their relationships with containers, files, and network.  This object relational format is highly compact but provides broad visibility into container clouds. We have also built several APIs that allow users to process SysFlow with their favorite toolkits.

Figure 1. provides an overview of all the components in the pipeline. SysFlow collectors are deployed to workload hosts and collect SysFlow, which is written as files to disk.  A SysFlow exporter, also deployed on each workload, pushes the files at intervals to an S3 compliant data store (like Cloud Object Store or COS).  From there, the Spark SysFlow analytics driver deploys Spark jobs at intervals to transform and analyze the SysFlow data. Our framework currently supports a rule based engine for analytics but is being expanded to support more AI based analytics.   Results from the analytics are written back to COS, or can be pushed to any desired data store, or messaging framework.  In Figure 1, we depict the analytic results being pushed to IBM's Cloud Security Advisor dashboard; however, the framework is designed to work with any cloud environment.    

We have also built Helm Charts to support the deployment of the pipeline on K8's clusters as daemonsets.   In the future, we hope to add additional deployment models such as OpenShift, and Docker Swarm.

.. figure:: _static/sysflowpipeline.png
   :scale: 50 %
   :alt: SysFlow Telemetry Pipeline
   
   Figure 1.: SysFlow Telemetry Pipeline

.. toctree::
   :maxdepth: 2
   :caption: Contents:
  
   sfcol
   helm

  
Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
