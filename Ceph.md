[[Data Engineering]] [[Data Lake]] [[LakeFS]]
# Intro
==Open source, highly scalable, highly robust software-defined storage solution designed to address the block, file, and object storage needs of modern enterprises==
- software abstraction layer **decouples data from physical storage hardware**, providing unparalleled scaling & fault management capabilities
- Ceph storage cluster requires at least one:
	- **Monitor** `ceph-mon`: maintains maps of the cluster state, including the monitor map, manager map, OSD map, MDS map, and the CRUSH map
		- these maps are critical cluster state required for seph daemons to coordinate with each other
		- monitors are also responsible for managing authentication between daemons & clients
		- *at least 3 monitors are required for redundancy & high availability*
	- **Managers** `ceph-mgr`: keeps track of runtime metrics & the current state of the Ceph cluster, including storage utilization, current performance metrics, and system load
		- the Ceph Manager daemons also host python-based modules to manage & expose Ceph cluster information, including a web-based Ceph Dashboard & REST API
		- *at least 2 managers are recommended for high availability*
	- **Ceph OSDs** `ceph-osd` (object storage daemon):
		- stores data
		- handles data replication, recovery, & rebalancing
		- provides some monitoring information to Ceph monitors & managers by checking other Ceph OSD Daemons for a heartbeat
		- *at least 3 Ceph OSDs are required for redundancy & high availability*
	- **MDSs** `ceph-mds` (MetaData Server):
		- stores metadata on behalf of the Ceph File System (Ceph Block Devices & Ceph Object Storage do not use MDS
		- enables POSIX file system users to execute basic commands like `ls` & `find` without placing an enormous burden on the Ceph Storage Cluster

# Architecture
- Ceph Storage Cluster recieves data from Ceph Clients (block devices, object storage, file system) & stores data as objects within logical storage **pools** on OSDs
	- OSDs handle read, write, and replication operations
- using the CRUSH algorithm, Ceph calculates:
	- which placement group should contain the object
	- and which OSD should store the placement group
- objects are stored in a monolithic, database-like fashion in a flat namespace
	- each object has globally unique ID, binary data, and metadata k/v pairs
		- metadata are determined by the Ceph Client (e.g. CephPS will store file owner, created date, last modified date, etc)
- 