#system-design 
# 1. Ambassador pattern
- imagine a busy CEO with a personal assistant who handles all your appointments and communication
- acts as a go-between for your app & the services it communicates with
	- offloads tasks like logging, monitoring, or handling retries
- helps reduce latency, enhances security, and improve the overall architecture of your distributed systems
	- e.g. Kubernetes --> Envoy

# 2. circuit breaker 
- imagine a water pipe bursting in your house
	- the first thing you would do would be to close off the main valve to prevent further damage
- when a system becomes unavailable, the circuit breaker stops requests, allowing it to recover

# CQRS (command query responsibility segregation)
- imagine a restaurant with separate lines for ordering food & picking up orders
- by separating the command/write operations from read/query operations
- separate read DB & write DB
	- scale & optimize each independently

# Event sourcing
- imagine keeping a journal of life events
	- instead of updating records directly, we store events representing changes
	- provides a complete history of the system 
		- better auditing & debugging
- e.g. git version control 

# Leader election
- class electing a class president
- nodes are then responsible only for a specific task
- when a leader node fails, the remaining nodes elect a new leader
- avoids conflicts & ensure consistent decision making across a distributed system

# Pub/sub
