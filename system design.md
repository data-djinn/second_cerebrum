[[Designing Data Intensive Applications]]

## functional requirements
- ==define system behavior==
	- what a system is supposed to **do**
		- e.g. the system must allow applications to exchange messages
	- captured in the form of a list of small set use cases (narrow down scope!)
	-  
## Non-functional requirements
- ==define **qualities** of a system==
	- how a system is supposed to **be**
		- e.g. scalable, highly available, fast

## High availability
- uptime = the % of time the system has been working & available
- OR "count based" = success ratio of requests
- 99% = 1 request out of 100 fails, or the system was unavailable for 3.65 days per year
- We really need the capability to apply software upgrades & security patches while maintaining high availability
### Requirements
1. Build redundancy to eliminate single points of failure
	1. regions, AZs, failbacks, data replication, high availability pairs, etc...
2. switch from one server to another without losing data
	1. DNS, load balancing, reverse proxies, API gateway, peer discovery, service discovery
3. protect the system from atypical client behavior
	1. lead shedding, rate limiting, shuffle sharding, cell-based architecture
4. protect the system from failures & performance degradation of its dependencies
	- timeouts, circuit breakers, bulkheads, retries, idempotency
5. detect failures as they occur
	1. monitoring...

### processes
1. change management
	1. all code & configuration changes are reviewed and approved
2. QA
	1. regularly exercise tests to validate that newly introduced changes meet functional & non-functional requirements
3. deployment
	1. deploy changes to a prod environment frequently, quickly, safely, automated rollback
4. capacity planning
	1. monitor system utilization and add resources to meet growing demand
5. disaster recovery
	1. recover system quickly in the event of a disaster; regularly test failover to disaster recovery
6. root cause analysis
	1. establish the root cause of the failure and identify preventative measures
7. operational readiness review
	1. evaluate system's operational state and identify gaps in operations, define actions to remediate risks
8. game day
	1. simulate a failure or event and test team responses

#### system knows how to handle expected failures (server crash, power outage, network disconnect)
###### Reliability
- system always performs its intended functions correctly and in time
######  high availability
- small downtime

#### system knows how to handle *unexpected* failures (server crash, power outage, network disconnect)
###### fault tolerance
- close to 0 downtime
###### resilience
- ability to quickly recover from failures


## Scalability
### Vertical scaling
- add resources:
	- CPU
	- MEMORY
	- DISK
### Horizontal scaling
- preferred
- effectively unlimited
- add more servers
- adds complexity:
	- Will now need:
		- service discovery
		- load balancing
		- request routing
		- multiple servers to maintain

#### elasticity: the ability of a system to acquire resources as it needs them, and release resources when it no longer needs them


## Performance
### Latency
- time required to process something
- response time = network delay + service time
	- total time = time spent "on the wire" bidirectionally + time taken for server to process the request
### Throughput
- rate at which something is processed
- 