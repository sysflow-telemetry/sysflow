.. SysFlow Telemetry Pipeline documentation master file, created by
   sphinx-quickstart on Wed Aug  7 16:34:09 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to SysFlow Telemetry Pipeline's documentation!
======================================================

Introduction
============

The SysFlow Telemetry Pipeline is a fully integrated framework for monitoring cloud workloads and for creating performance and security analytics. The goal of the work is to build all the plumbing required for system telemetry, so that users can focus on writing and sharing analytics on a scalable common open source platform.  The backbone of the telemetry pipeline is a new data format called SysFlow which lifts raw system event information into an abstraction that describes process behaviors, and their relationships with containers, files, and network.  This object relational format is highly compact but provides broad visibility into container clouds. We have also built several APIs that allow users to process SysFlow with their favorite toolkits.

Figure 1. provides an overview of all the components in the pipeline. SysFlow collectors (sf-collector project) are deployed to workload hosts and collect SysFlow, which is written as files to disk.  A SysFlow exporter (sf-exporter project), also deployed on each workload, pushes the files at intervals to an S3 compliant data store (like Cloud Object Store or COS).  From there, the Spark SysFlow analytics driver (sf-analytics project) deploys Spark jobs at intervals to transform and analyze the SysFlow data. Our framework currently supports a rule based engine (sf-falco project) for analytics but is being expanded to support more AI based analytics.   Results from the analytics are written back to COS, or can be pushed to any desired data store, or messaging framework.  In Figure 1, we depict the analytic results being pushed to IBM's Cloud Security Advisor dashboard (sf-findings project); however, the framework is designed to work with any cloud environment. 

.. figure:: _static/sysflowpipeline.png
   :scale: 50 %
   :alt: SysFlow Telemetry Pipeline
   
   Figure 1.: SysFlow Telemetry Pipeline

The pipeline can currently be deployed in Docker containers, through Docker Swarm, or to a K8's cluster through helm charts (see sf-deployments project).  We hope to add other deployment options such as OpenShift in the future.

Lastly, there are C++ and python apis available in the sf-apis project that allow users to interact with SysFlow Files directly.  There are also Apache Avro schema files for SysFlow so that users can generate API's for any number of other languages such as golang or JAVA.

To learn more about each project, please see the table of contents below, or visit the README's in each project's git repo.


.. toctree::
   :maxdepth: 2
   :caption: Contents:
  
   sfcol
   exporter
   api-utils
   sfanalytics
   deploy
   findings
   cards
  
Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
