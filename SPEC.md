<a name="sysflow"></a>
# SysFlow Specification

SysFlow is an open specification for system event-level telemetry.  The main goal of SysFlow is to create a standard and extensible data format for both security and performance analytics for compute workloads.  An open standard will enable researchers and practitioners to more easily work on a common data format, and focus on analytics using open source software.   

The primary objective of SysFlow is to lift raw system call data into more semantic process behaviors which promote significant data reductions for longer term forensic storage of data which is crucial for security analyzes.  Through an object relational model of entities, events and flows, we enable SysFlow users to configure the desired granularity of data collection and filtering in order to facilitate most types of analysis in big data frameworks.          

*  [SysFlow Overview](#overview)
* [Entities](#entities)
    * [Header](#header)
    * [Container](#container)
    * [Process](#process)
    * [File](#file)
* [Events](#events)
    * [Operation Flags](#oflags)
    * [ProcessEvent](#procevent)
    * [FileEvent](#fileevent)
    * [NetworkEvent](#netevent)
* [Flows](#flows)
    * [ProcessFlow](#procflow)
    * [FileFlow](#fileflow)
    * [NetworkFlow](#netflow)

<a name="overview"></a>
## SysFlow Overview 

SysFlow is an object relational model of entities, events and flows that describe the behaviors of processes on a system, and encode them into an open format.  A SysFlow exporter is designed to monitor system events of a workload, convert them to SysFlow objects, and output them in a binary output file.  We envision that one exporter will be deployed per host (or Virtual Machine) and will output one binary file over a particular time period.   Figure 1 show a detailed view of the objects that the SysFlow exporter will export. 

Entities represent the components on a system that we are interested in monitoring.   In this version of SysFlow, we support three types of entities: Containers, Processes, and Files.  As shown in Figure 1, Containers contain both Processes and Files, and the three are linked through object identifiers (more on this later). 

Entity behaviors are modeled as events or flows.  Events represent important individual behaviors of an entity that are broken out on their own due to their importance, their rarity, or because maintaining operation order is important. An example of an event would be a process clone or exec, or the deletion or renaming of a file.  By contrast, a Flow represents an aggregation of multiple events that naturally fit together to describe a particular behavior. For example, we can model the network interactions of a process and a remote host as a bidirectional flow that is composed of several events, including connect, read, write, and close. 

The idea behind SysFlow is to enable the user to configure the granularity of system-level data desired based on resource limitations and data analytics requirements.   In this way, behaviors can be broken out into individual events or combined into smaller aggregated volumetric flows.  The current version of the specification describes events and flows in three key behavioral areas:  Files, Networks, and Processes.   Figure 1 shows these events and flows with their attributes and relationships to entities, which are described in greater details in the following sections.

![SF_Object_View.png](/_static/SF_Object_View.png)
*Figure 1:  SysFlow Object Relational View*

<a name="entities"></a>
### Entities
As mentioned above, entities are the components on a system that we are interested in monitoring. These include containers, processes, and files.  We also support a special entity object called a Header, which stores information about the SysFlow version, and a unique ID representing the host or virtual machine monitored by the SysFlow exporter.  The header is always the first record appearing in a SysFlow File. All other entities contain  a timestamp, an object ID and a state.   The timestamp is used to indicate the time at which the entity was exported to the SysFlow file. 

#### Object ID
Object IDs allow events and flows to reference entities without having duplicate information stored in each record.  Object IDs are not required to be globally unique across space and time.  In fact, the only requirement for uniqueness is that no two objects managed by a SysFlow exporter can have the same ID simultaneously.  Entities are always written to the binary output file before any events, and flows associated with them are exported. Since entities are exported first, each event, and flow is matched with the entity (with the same id) that is closest to it in the file. Furthermore, every binary output file must be self-contained, meaning that all entities referenced by flows/events must be present in every SysFlow file generated.  

#### State

The state is an enumeration that indicates why an entity was written to disk.   The state can currently be one of three values:

| State     | Description  | 
| ------------- |:-------------:| 
| CREATED |  Indicates that the entity was recently created on the host/VM. For example, a process clone. |
| MODIFIED | Indicates that some attributes of the entity were modified since the last time it was exported. |
| REUP | Indicates that the entity already existed, but is being exported again, so that output files can be self-contained. |
 


Each entity is defined below with recommendations on what to use for object identifiers, based on what is used in the current implementation of the SysFlow exporter.    

   

<a name="header"></a> 
#### Header 

The Header entity is an object which appears at the beginning of each binary SysFlow file.   It contains the current version of SysFlow as supported in the file, and the exporter ID.

| Attribute     | Type           | Description  | Status |
| ------------- |:-------------:| -----|  ----- |
| version | long |  The current SysFlow version. | Implemented  |
| exporter | string |  Globally unique id representing the host monitored by SysFlow | Implemented  |

<a name="container"></a>
#### Container

The Container entity represents a system or application container such as docker or LXC.   It contains important information about the container including its id, name, and whether it is privileged.

 
| Attribute     | Type           | Description  | Status |
| ------------- |:-------------:| -----|  ----- |
| id | string |  Unique string representing the Container Object as provided by docker, LXC, etc. | Implemented (Should we rename to oid?)  |
| **state**      | enum | state of the process (CREATED, MODIFIED, REUP) | **NOT IMPLEMENTED** Do we want this? |
| **timestamp (ts)**|  int64 | The timestamp when container object is exported (nanoseconds). | **NOT IMPLEMENTED** Do we want this? |
| name | string |  Container name as provided by docker, LXC, etc. | Implemented |
| image | string |  Image name associated with container as provided by docker, LXC, etc. | Implemented |
| imageID | string |  Image ID associated with container as provided by docker, LXC, etc. | Implemented |
| type | enum |  Can be one of: CT_DOCKER, CT_LXC, CT_LIBVIRT_LXC, CT_MESOS, CT_RKT, CT_CUSTOM. | Implemented |
| privileged | boolean |  If true, the container is running with root privileges | Implemented |

<a name="process"></a>
#### Process 

The process entity represents a running process on the system.  It contains important information about the process including its host pid, creation time, oid id, as well as references to its parent id. When a process entity is exported to a SysFlow file, all its parent processes should be exported before the process, as well as the process's Container entity.   Processes are only exported to a SysFlow file if an event or flow associated with that process or any of its threads are exported.  Threads are not explicitly exported in the process object but are represented in events and flows through a thread id field. Finally, a Process entity only needs to be exported to a file once, unless it's been modified by an event or flow.

NOTE:  In current implementation, the creation timestamp is the time at which the process is cloned.  If the process was cloned before capture was started, this value is 0.  The current implementation also has problems getting absolute paths for exes when relative paths are used to launch processes.

| Attribute     | Type           | Description  | Status |
| ------------- |:-------------:| -----|  ----- |
| state      | enum | state of the process (CREATED, MODIFIED, REUP) | Implemented |
| **OID:**<br> &nbsp;&nbsp;*host pid*<br>&nbsp;&nbsp;*create ts*| **struct** <br> &nbsp;&nbsp;*int64*<br>&nbsp;&nbsp;*int64*| The Process OID contains the host pid of the project, and creation timestamp. | Implemented |
| **POID:**<br> &nbsp;&nbsp;*parent host &nbsp;&nbsp;pid*<br>&nbsp;&nbsp;*parent &nbsp;create ts* | **struct** <br> &nbsp;&nbsp;*int64*<br>&nbsp;&nbsp;*int64*| The OID of the parent process can be NULL if not available or if a root process. | Implemented |
| timestamp (ts)|  int64 | The timestamp when process object is exported (nanoseconds). | Implemented |
| exe |  string |  Full path (if available) of the executable used in the process launch. Otherwise, it's the name of the exe. | Implemented |
| exeArgs |  string |  Concatenated list of args passed on process startup. | Implemented |
| uid |  int32 |  User ID under which the process is running. | Implemented |
| userName |  string |  User name under which the process is running. | Implemented |
| gid |  int32 |  Group ID under which the process is running. | Implemented |
| groupName |  string |  Group Name under which the process is running. | Implemented |
| tty |  boolean |  If true, the process is tied to a shell | Implemented |
| containerId | string |  Unique string representing the Container Object to which the process resides.  Can be NULL if process isn't in a container. | Implemented
 
<a name="file"></a>
#### File

The File entity represents file-based resources on a system including files, directories, unix sockets, and pipes.

NOTE: Current implementation does not have access to inode related values, which would greatly improve object ids.   Also, the current implementation has some issues with absolute paths when monitoring operations that use relative paths.


| Attribute     | Type           | Description  | Status |
| ------------- |:-------------:| -----|  ----- |
| **state**      | enum | state of the file (CREATED, MODIFIED, REUP) | Implemented |
| **FOID:** |  string (128bit) | File Identifier, is a SHA1 hash of the concatenation of the path + container ID | Implemented |
| timestamp (ts)|  int64 | The timestamp when file object is exported (nanoseconds). | Implemented |
| restype |   enum | Indicates the resource type.  Currently support: SF_FILE, SF_DIR, SF_UNIX (unix socket), SF_PIPE, SF_UNKNOWN | Implemented |
| path |   string | Full path of the file/directory, or unique identifier for pipe, unix socket | Implemented |
| containerId | string |  Unique string representing the Container Object to which the file resides.  Can be NULL if file isn't in a container. | Implemented

<a name="events"></a>
### Events 
Events represent important individual behaviors of an entity that are broken out on their own due to their importance, their rarity, or because maintaining operation order is important.  In order to manage events and their differing attributes, we divide them into three different categories:  Process, File, and Network events.  These are described more in detail later on.  

Each event and flow contains a process object id, a timestamp, a thread id, and a set of operation flags.  The process object id represents the Process Entity on which the event occurred, while the thread id indicates which process thread was associated with the event.

<a name="oflags"></a>
#### Operation Flags
The operation flags describe the actual behavior associated with the event (or flow).  The flags are represented in a single bitmap which enables multiple behaviors to be combined easily into a flow.   An event will have a single bit active, while a flow could have several. The current supported flags are as follows:


| Operation     | Numeric ID  | Description |System Calls | Evts/Flows Supported |
| ------------- | ------------- | ----------- |  ----- | ------- |
| OP_CLONE      | (1 << 0) | Process or thread cloned. | clone() | ProcessEvent |
| OP_EXEC       | (1 << 1) | Execution of a file| execve() | ProcessEvent |
| OP_EXIT       | (1 << 2) | Process or thread exit. | exit() | ProcessEvent |
| OP_SETUID     | (1 << 3) | UID of process was changed | setuid(), setresuid | ProcessEvent |
| OP_SETNS      | (1 << 4) | Process entering namespace | setns() | FileFlow |
| OP_ACCEPT     | (1 << 5) | Process accepting network connections | accept(), select() | NetworkFlow |
| OP_CONNECT    | (1 << 6) | Process connecting to remote host or process | connect() | NetworkFlow |
| OP_OPEN       | (1 << 7) | Process opening a file/resource | open(), openat(), create() | FileFlow |
| OP_READ_RECV  | (1 << 8) | Process reading from file, receiving network data | read(),pread(),recv(),recvfrom(),recvmsg() | NetworkFlow, FileFlow |
| OP_WRITE_SEND | (1 << 9) | Process writing to file, sending network data | write(),pwrite(),send(),sendto(),sendmsg() | NetworkFlow, FileFlow |
| OP_CLOSE      | (1 << 10)| Process close resource | close(),socketshutdown | NetworkFlow, FileFlow |
| OP_TRUNCATE   | (1 << 11)| Premature closing of a flow due to exporter shutdown | N/A| NetworkFlow, FileFlow |
| OP_DIGEST     | (1 << 12)| Summary flow information for long running flows | N/A | NetworkFlow, FileFlow |
| OP_MKDIR      | (1 << 13)| Make directory | mkdir(), mkdirat() | FileEvent|
| OP_RMDIR      | (1 << 14)| Remove directory | rmdir() | FileEvent|
| OP_LINK       | (1 << 15)| Process creates hard link to existing file | link(), linkat() | FileEvent|
| OP_UNLINK     | (1 << 16)| Process deletes file | unlink(), unlinkat() | FileEvent|
| OP_SYMLINK    | (1 << 17)| Process creates sym link to existing file | symlink(), symlinkat() | FileEvent|
| OP_RENAME     | (1 << 18) | File renamed | rename(), renameat() | FileEvent|

    
<a name="procevent"></a>
#### Process Event 

A Process Event is an event that creates or modifies a process in some way.   Currently, we support four Process Events (referred to as operations), and their behavior in SysFlow is described below.  For more information on operations see ([Operation Flags](#oflags)).  

| Operation     | Behavior  |
| ------------- | -----------| 
| OP_CLONE      | Exported when a new process or thread is cloned.  A new Process Entity should be exported prior to exporting the clone operation of a new process. |
| OP_EXEC       | Exported when a process calls an exec syscall.  This event will modify an existing process, and should be accompanied by a modified Process Entity. |
| OP_EXIT       | Exported on a process or thread exit. |
| OP_SETUID     | Exported when a process's UID is changed. This event will modify an existing process, and should be accompanied by a modified Process Entity. **NEED TO CHECK THIS** |

The list of attributes for the Process Event are as follows:

| Attribute     | Type           | Description  | Status |
| ------------- |:-------------:| -----|  ----- |
| **OID:**<br> &nbsp;&nbsp;*host pid*<br>&nbsp;&nbsp;*create ts*| **struct** <br> &nbsp;&nbsp;*int64*<br>&nbsp;&nbsp;*int64*| The OID of the process for which the event occurred. | Implemented |
| timestamp (ts)|  int64 | The timestamp when the event occurred (nanoseconds). | Implemented |
| tid |  int64 | The id of the thread associated with the ProcessEvent.  If the running process is single threaded tid == pid | Implemented |
| opFlags | int64 | The id of the syscall associated with the event.  See list of Operation Flags for details. | Implemented | 
| args |  string[] | An array of arguments encoded as string for the syscall. |  Sparingly implemented. Only really used with setuid for now. |
| ret | int64 | Syscall return value. | Implemented | 
<a name="fileevent"></a>
#### File Event 

A File Event is an event that creates, deletes or modifies a File Entity.   Currently, we support six File Events (referred to as operations), and their behavior in SysFlow is described below.  For more information on operations see ([Operation Flags](#oflags)).

| Operation     | Behavior  |
| ------------- | -----------| 
| OP_MKDIR      | Exported when a new directory is created.  Should be accompanied by a new File Entity representing the directory |
| OP_RMDIR      | Exported when a directory is deleted.|
| OP_LINK       | Exported when a process creates a hard link to an existing file.  Should be accompanied by a new File Entity representing the new link. |
| OP_UNLINK     | Exported when a process deletes a file. |
| OP_SYMLINK    | Exported when a process creates a sym link to an existing file.  Should be accompanied by a new File Entity representing the new link.|
| OP_RENAME     | Exported when a process creates renames an existing file.  Should be accompanied by a new File Entity representing the renamed file.|

NOTE:   We'd like to also support **chmod** and **chown** but these two operations are not fully supported in sysdig. We'd also like to support **umount** and **mount** but these operations are not implemented.

The list of attributes for the File Event are as follows:

| Attribute     | Type           | Description  | Status |
| ------------- |:-------------:| -----|  ----- |
| **OID:**<br> &nbsp;&nbsp;*host pid*<br>&nbsp;&nbsp;*create ts*| **struct** <br> &nbsp;&nbsp;*int64*<br>&nbsp;&nbsp;*int64*| The OID of the process for which the event occurred. | Implemented |
| timestamp (ts)|  int64 | The timestamp when the event occurred (nanoseconds). | Implemented |
| tid |  int64 | The id of the thread associated with the FileEvent.  If the running process is single threaded tid == pid | Implemented |
| opFlags | int64 | The id of the syscall associated with the event.  See list of Operation Flags for details. | Implemented | 
| ret | int64 | Syscall return value. | Implemented | 
| **FOID:** |  string (128bit) | The id of the file on which the system call was called. File Identifier, is a SHA1 hash of the concatenation of the path + container ID. | Implemented |
| **NewFOID:** |  string (128bit) | Some syscalls (link, symlink, etc.) convert one file into another requiring two files. This id is the id of the file secondary or new file on which the system call was called. File Identifier, is a SHA1 hash of the concatenation of the path + container ID. Can be NULL. | Implemented |

<a name="netevent"></a>
#### NetworkEvent 

Currently, NOT IMPLEMENTED.
<a name="flows"></a>
### Flows 

A Flow represents an aggregation of multiple events that naturally fit together to describe a particular behavior.   They are designed to reduce data and collect statistics.   Examples of flows include an application reading or writing to a file, or sending and receiving data from another process or host.    Flows represent a number of events occurring over a period of time, and as such each flow has a set of operations (encoded in a bitmap), a start and an end time.   One can determine the operations in the flow by decoding the operation flags.

A flow can be started by any supported operation and are exported in one of two ways.   First, they are exported on an exit, or close event signifying the end of a connection, file interaction, or process.  Second, a long running flow is exported after a preconfigured time period. After a long running flow is exported, its counters and flags are reset. However, if there is no activity on the flow over a preconfigured period of time, that flow is no longer exported.    

In this section, we describe three categories of Flows:  Process, File and Network Flows.

<a name="procflow"></a>
#### ProcessFlow 

Currently, NOT IMPLEMENTED.

<a name="fileflow"></a>
#### FileFlow 

A File Flow represents a collection of operations on a file.   Currently we support the following operations (for more information on operations see ([Operation Flags](#oflags))):

| Operation     | Behavior  |
| ------------- | -----------| 
| OP_SETNS      | Process entering namespace entry in mounted file related to reference File Entity |
| OP_OPEN       | Process opening a file/resource. |
| OP_READ_RECV  | Process reading from file/resource.|
| OP_WRITE_SEND | Process writing to file. |
| OP_CLOSE      | Process closing resource. This action will close corresponding FileFlow. |
| OP_TRUNCATE   | Indicates Premature closing of a flow due to exporter shutdown.|
| OP_DIGEST     | Summary flow information for long running flows. **NOT CURRENTLY IMPLEMENTED**. |

The list of attributes for the File Flow are as follows:


| Attribute     | Type           | Description  | Status |
| ------------- |:-------------:| -----|  ----- |
| **OID:**<br> &nbsp;&nbsp;*host pid*<br>&nbsp;&nbsp;*create ts*| **struct** <br> &nbsp;&nbsp;*int64*<br>&nbsp;&nbsp;*int64*| The OID of the process for which the flow occurred. | Implemented |
| timestamp (ts)|  int64 | The timestamp when the flow starts (nanoseconds). | Implemented |
| tid |  int64 | The id of the thread associated with the flow.  If the running process is single threaded tid == pid | Implemented |
| opFlags | int64 (bitmap) | The id of one or more syscalls associated with the FileFlow.  See list of Operation Flags for details. | Implemented | 
| openFlags | int64 | Flags associated with an open syscall if present. | Implemented |
| endTs |  int64 | The timestamp when the file flow is exported (nanoseconds). | Implemented | 
| **FOID:** |  string (128bit) | The id of the file on which the system call was called. File Identifier, is a SHA1 hash of the concatenation of the path + container ID. | Implemented |
| fd |  int32 | The file descriptor associated with the flow. | Implemented |
| numRRecvOps | int32 | Number of read operations performed during duration of the flow. | Implemented |
| numWSendOps | int32 | Number of write operations performed during duration of the flow. | Implemented |
| numRRecvBytes | int32 | Number of bytes read during duration of the flow. | Implemented |
| numWSendBytes | int32 | Number of bytes written during duration of the flow. | Implemented | 
<a name="networkflow"></a>
#### NetworkFlow 

A Network Flow represents a collection of operations on a network connection.   Currently we support the following operations (for more information on operations see ([Operation Flags](#oflags))):

| Operation     | Behavior  |
| ------------- | -----------| 
| OP_ACCEPT     | Process accepted a new network connection. |
| OP_CONNECT    | Process connected to a remote host or process.  |
| OP_READ_RECV  | Process receiving data from a remote host or process.|
| OP_WRITE_SEND | Process sending data to a remote host or process.|
| OP_CLOSE      | Process closing network connection. This action will close corresponding NetworkFlow. |
| OP_TRUNCATE   | Indicates Premature closing of a flow due to exporter shutdown.|
| OP_DIGEST     | Summary flow information for long running flows. **NOT CURRENTLY IMPLEMENTED**. |

The list of attributes for the Network Flow are as follows:

| Attribute     | Type           | Description  | Status |
| ------------- |:-------------:| -----|  ----- |
| **OID:**<br> &nbsp;&nbsp;*host pid*<br>&nbsp;&nbsp;*create ts*| **struct** <br> &nbsp;&nbsp;*int64*<br>&nbsp;&nbsp;*int64*| The OID of the process for which the flow occurred. | Implemented |
| timestamp (ts)|  int64 | The timestamp when the flow starts (nanoseconds). | Implemented |
| tid |  int64 | The id of the thread associated with the flow.  If the running process is single threaded tid == pid | Implemented |
| opFlags | int64 (bitmap) | The id of one or more syscalls associated with the flow.  See list of Operation Flags for details. | Implemented | 
| endTs |  int64 | The timestamp when the flow is exported (nanoseconds). | Implemented | 
| sip |  int32 | The source IP address. | Implemented |
| sport |  int16 | The source port. | Implemented |
| dip |  int32 | The destination IP address. | Implemented |
| dport |  int16 | The destination port. | Implemented |
| proto |  enum | The network protocol of the flow.  Can be: TCP, UDP, ICMP, RAW | Implemented |
| numRRecvOps | int32 | Number of receive operations performed during duration of the flow. | Implemented |
| numWSendOps | int32 | Number of send operations performed during duration of the flow. | Implemented |
| numRRecvBytes | int32 | Number of bytes received during duration of the flow. | Implemented |
| numWSendBytes | int32 | Number of bytes sent during duration of the flow. | Implemented | 

NOTE:  The current implementation of NetworkFlow only supports ipv4.  What's the best way to add in ipv6?

