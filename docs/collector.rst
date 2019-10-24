SysFlow Collector (sf-collector repo)
========================================

The SysFlow Collector monitors and collects system call and event information from hosts
and exports them in the SysFlow format using Apache Avro object serialization.  SysFlow lifts
system call information into a higher order object relational form that models how containers, 
processes and files interact with their environment through process control flow, file, and network
operations.  Learn more about SysFlow in the SysFlow Specification Document (below).

The SysFlow Collector is currently built upon a Sysdig core and requires the Sysdig probe to passively
collect system events and turn them into SysFlow.  As a result, the collector supports Sysdig's powerful
filtering capabilities.  Please see the build and installation instructions for installing the collector.


.. toctree::
   :maxdepth: 2
    
   build
   spec
