[[postgres]]
![[Pasted image 20211229175718.png]]
![[Pasted image 20211229175900.png]]
- Users/application connects to a PostgreSQL process
    - process forks & interacts with buffors in the shared memory (cached)
    - as many operations are calculated in-memory & then flushed off to disk
    - data transactions occur in the shared buffers
    - write-ahead buffers
        - allow us to maintain data integrity (ACID)
        - records every transaction to the log
        - like a stenographer
- utility processes
- Writer "hardens" the data, writing the changes to disk
    - clears memory
- Checkpointer is a point in the transaction where all the data files have been updated in the transactions in the log
- logs used for troubleshooting
- stats collects activity on the server
- **autovacuum** multiversion concurrency control creates a lot of garbage
    - also runs `analyze` to collect table statistics for the query plan

# Clustering
![[Pasted image 20211230114414.png]]
### Replication
- replication is included in postgres by default
    - master server is the only one that can perform read + write
    - **hot standby**
        - replication can be read by external clients
        - transactions are copied to it
    - **warm standby**
        - same type replication, on standby
        - new clients can read as well
- if the master server goes down, the standby will fill in
- **Synchronous mode**
    - use when you need standby available in an instant (high availability)
- **Async mode**
    - changes are applied 
    - used when the network may not support full copies

### Load balancing (Horizontal scaling)
- client & applications are connecting to **1 shared virtual IP address**
    - from there, the queries are bounced to 1 of the active servers
    - each "node" is operating as a master node
        - read & write
        - synchronized

#### Replication is much easier to set up
- set up master-standby setup easily, achieving high-availability architecture with minimal effort
- Load balancing is much more difficult to maintain