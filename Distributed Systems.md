[[Kubernetes]]
==group of computers working together as to appear as a single computer to the end-user==
- shared state
- operate concurrently
- fail independently without affecting the whole system's uptime

### Why?
- enables you to scale *horizontally*
![[Pasted image 20230223195734.png]]
- no cap on how much you can scale - when performance degrades, you simply add another machine
##### Fault Tolerance
- a cluster of 10 machines across 2 data centers is inherently more fault-tolerant than a single machine
- even if one data center catches on fire, your application will still work
##### Low Latency

## Cap Theorum
**Choose 2:**
- consistency: sequencial write and read is immediately reflected across all nodes
- availability: the whole system does not die
- partition tolerant: system continues to function & uphold its consistency/availability guarantees in spite of network partition

in distributed systems, you can only choose 1 because you **need** partition tolerance
- most applications choose high availability
- network latency can be an issue when it comes to achieving strong consistency
##### Eventual consistency
- guaruntees that if no new updates are made to a given item, eventually all accesses to that item will return the latest updated value
##### BASE (vs. ACID)
- **Basically Available**: system always returns a response
- **Soft state**: system can change over time, even during times of no input (due to eventual consistency)
- **Eventual consistency**: in the absence of input, the data will spread to every node sooner or later
- examples are cassandra, mongo, cosmosdb, 

## Fundamental problem: *consensus*
- DB transactions are difficult to implement in distributed systems
- this is because they require each node to agree on the right action to take (abort or commit)

# Distributed Computing
### MapReduce
- map the data
	- seperate node transforms as much data as it can
	- each job traverses all the data in the storage node and maps it to a simple tuple of the 
- reduce it to something meaningful
- **problems:**
	- if any job fails, you have to restart the entire pipeline
	- also, you have to wait till the end of the job to see results
	- Lambda & Kappa architectures are more modern 
		- kafka, spark, storm, etc

