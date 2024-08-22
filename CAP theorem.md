#system-design

# Consistency
- all nodes have a consistent view of the data
- all writes are either reflected in all nodes, or throw an error
- all clients see the latest updates, no matter which node they connect to
# Availability
- ability of a system to respond to requests from users at all times
# (network) Partition Tolerance
- ability to continue operating even if there is a network partition
### What is a network partition?
- a network partition happens when nodes in a distributed system are unable to communicate with eachother due to network failures
- when there is a network partition, a system must choose between consistency & availability
	- if a system prioritizes consistency, it may become unavailable until the partition is resolved
	- if the system prioritized availability, it may allow updates to the data
		- this could result in data inconsistency until the network partition is resolved