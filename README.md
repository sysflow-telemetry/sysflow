# SysFlow Documentation
[![Documentation Status](https://readthedocs.org/projects/sysflow/badge/?version=latest)](https://sysflow.readthedocs.io/en/latest/?badge=latest)

This repository aggregates the [documentation](https://sysflow.readthedocs.io/) from the different SysFlow projects and hosts SysFlow's [issue tracker](https://github.com/sysflow-telemetry/sf-docs/issues).

# Quick reference

-	**Documentation**:  
	[the SysFlow Documentation](https://sysflow.readthedocs.io)
  
-	**Where to get help**:  
	[the SysFlow Community Slack](https://join.slack.com/t/sysflow-telemetry/shared_invite/enQtODA5OTA3NjE0MTAzLTlkMGJlZDQzYTc3MzhjMzUwNDExNmYyNWY0NWIwODNjYmRhYWEwNGU0ZmFkNGQ2NzVmYjYxMWFjYTM1MzA5YWQ)

-	**Where to file issues**:  
	[the github issue tracker](https://github.com/sysflow-telemetry/sysflow/issues)

-	**Source of this description**:  
	[repo's readme](https://github.com/sysflow-telemetry/sysflow/edit/master/README.md) ([history](https://github.com/sysflow-telemetry/sysflow/commits/master))

# What is SysFlow?

The SysFlow Telemetry Pipeline is a framework for monitoring cloud workloads and for creating performance and security analytics. The goal of this project is to build all the plumbing required for system telemetry so that users can focus on writing and sharing analytics on a scalable, common open-source platform. The backbone of the telemetry pipeline is a new data format called SysFlow, which lifts raw system event information into an abstraction that describes process behaviors, and their relationships with containers, files, and network. This object-relational format is highly compact, yet it provides broad visibility into container clouds. We have also built several APIs that allow users to process SysFlow with their favorite toolkits. Learn more about SysFlow in the [SysFlow specification document](https://sysflow.readthedocs.io/en/latest/spec.html).

The SysFlow framework consists of the following sub-projects:

- [sf-apis](https://github.com/sysflow-telemetry/sf-apis) provides the SysFlow schema and programatic APIs in go, python, and C++.
- [sf-collector](https://github.com/sysflow-telemetry/sf-collector) monitors and collects system call and event information from hosts and exports them in the SysFlow format using Apache Avro object serialization.
- [sf-processor](https://github.com/sysflow-telemetry/sf-processor) provides a performance optimized policy engine for processing, enriching, filtering SysFlow events, generating alerts, and exporting the processed data to various targets.
- [sf-exporter](https://github.com/sysflow-telemetry/sf-exporter) exports SysFlow traces to S3-compliant storage systems for archival purposes.
- [sf-deployments](https://github.com/sysflow-telemetry/sf-deployments) contains deployment packages for SysFlow, including Docker, Helm, and OpenShift.
- [sysflow](https://github.com/sysflow-telemetry/sysflow) is the documentation repository and issue tracker for the SysFlow framework.

## Online documentation
SysFlow documentation is available at [sysflow.readthedocs.io](https://sysflow.readthedocs.io/). 

## Offline build
This documentation depends on Sphinx (http://www.sphinx-doc.org/en/master/), which must be installed to do builds. The project also requires the following Sphinx plugins:

* http://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html
* https://pypi.org/project/m2r/

To build the site as HTML go to the base directory and type:
```
make html
```
