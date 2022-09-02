SysFlow Telemetry Pipeline
======================================================

The SysFlow Telemetry Pipeline is a framework for monitoring cloud and enterprise workloads. The framework builds the plumbing required for system telemetry so that users can focus on writing and sharing analytics on a scalable, common open-source platform.

.. note:: If in a hurry, skip to our `quick start <https://sysflow.readthedocs.io/en/latest/quick.html>`_ guide.

The backbone of the telemetry pipeline is a new `data format <https://sysflow.readthedocs.io/en/latest/spec.html>`_ which lifts raw system event information into an abstraction that describes process behaviors, and their relationships with containers, files, and network activity. This object-relational format is highly compact, yet it provides broad visibility into legacy endpoints and container clouds.

The platform is designed as a pluggable edge processing architecture which includes a policy engine that accepts declarative policies that support edge filtering, tagging, and alerting on SysFlow streams. It also offers several APIs that allow users to process SysFlow with their favorite toolkits.

The pipeline can be `deployed <https://sysflow.readthedocs.io/en/latest/deploy.html>`_ using Docker, Kubernetes, OpenShift, and bare metal/VMs. The `SysFlow agent <https://sysflow.readthedocs.io/en/latest/quick.html#deployment-options>`_ can be configured as an edge analytics pipeline to stream SysFlow records through rsyslog, or as a batch exporter of raw SysFlow traces to S3-compatible object stores.

An integrated `Jupyter environment <https://sysflow.readthedocs.io/en/latest/quick.html#analyzing-collected-traces>`_ makes it easy to perform log hunting on collected traces. There are also Apache Avro schema files for SysFlow so that users can generate APIs for other programming languages. C++, Python, and Golang `APIs <https://github.com/sysflow-telemetry/sf-apis>`_ are available, allowing users to interact with SysFlow traces programmatically.

To learn more about SysFlow, check the table of contents below.

*We welcome feedback, bug reports, and contributions!*

Keep in touch
-------------
Please connect with us on our `Slack <https://join.slack.com/t/sysflow-telemetry/shared_invite/enQtODA5OTA3NjE0MTAzLTlkMGJlZDQzYTc3MzhjMzUwNDExNmYyNWY0NWIwODNjYmRhYWEwNGU0ZmFkNGQ2NzVmYjYxMWFjYTM1MzA5YWQ>`_ community!

Bugs & Feature requests
-------------
For bugs and feature requests, please check our `issue tracker <https://github.com/sysflow-telemetry/sf-docs/issues>`_.

License
-------
SysFlow and all projects are released under the Apache v2.0 license.

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   quick
   spec
   collector
   processor
   exporter
   api-utils
   deploy
   libs
   license
   contributing
   coc
   publications

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
