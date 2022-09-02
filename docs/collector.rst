SysFlow Collector (sf-collector repo)
========================================

The SysFlow Collector monitors and collects system call and event information from hosts
and exports them in the SysFlow format using Apache Avro object serialization. It's built atop
libSysFlow, a library that lifts system call information into SysFlow, a higher order object
relational form that models how containers, processes and files interact with their environment
through process control flow, file, and network operations. Learn more about SysFlow in the
SysFlow Specification Document.

The SysFlow Collector builds on the `CNCF Falco libs <https://github.com/falcosecurity/libs>`_ to
passively collect system events and turn them into SysFlow. As a result, the collector supports the
libs' powerful filtering capabilities. Check the build and installation instructions for installing
the collector.

.. toctree::
   :maxdepth: 2

   libs
   build

