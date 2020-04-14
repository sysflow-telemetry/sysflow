SysFlow Telemetry Pipeline Documentation
======================================================

Introduction
------------

The SysFlow Telemetry Pipeline is a framework for monitoring cloud workloads and for creating performance and security analytics. The goal of this project is to build all the plumbing required for system telemetry so that users can focus on writing and sharing analytics on a scalable, common open-source platform. The backbone of the telemetry pipeline is a new data format called SysFlow, which lifts raw system event information into an abstraction that describes process behaviors, and their relationships with containers, files, and network. This object-relational format is highly compact, yet it provides broad visibility into container clouds. We have also built several APIs that allow users to process SysFlow with their favorite toolkits.

The pipeline can currently be deployed using docker or kubernetes through helm charts (see sf-deployments project). We plan to support other deployment options, such as OpenShift in the future. Contributions are welcome!

Lastly, C++ and Python APIs are available in the sf-apis project, allowing users to interact with SysFlow traces programmatically. There are also Apache Avro schema files for SysFlow so that users can generate APIs for other languages, such as golang or JAVA.

To learn more about each project, please check the table of contents below, or visit the READMEs in each project's git repo.

*This an ongoing research project. We welcome feedback, bug reports, and contributions!*

Keep in touch
-------------
Please connect with us on our `Slack <https://join.slack.com/t/sysflow-telemetry/shared_invite/enQtODA5OTA3NjE0MTAzLTlkMGJlZDQzYTc3MzhjMzUwNDExNmYyNWY0NWIwODNjYmRhYWEwNGU0ZmFkNGQ2NzVmYjYxMWFjYTM1MzA5YWQ>`_ community! For bugs and feature requests, please check our `issue tracker <https://github.com/sysflow-telemetry/sf-docs/issues>`_. 


License
-------
SysFlow and all projects are released under the Apache v2.0 license.

.. toctree::
   :maxdepth: 2
   :caption: Contents:
   
   quick
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
