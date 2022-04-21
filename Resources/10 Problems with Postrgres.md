[[postgres]]

   

1.  XID wraparound

1.  32-bit IDs bad, 64-bit IDs good

3.  Failover will lose data
4.  Inefficient replication
5.  MVCC garbage frequently painful

1.  VACUUM (auto) works most of the time

1.  Process-per-connection = pain at scale

1.  Adding 2x cores (connections) performance will suffer

1.  Primary Key Index is a space hog
  - Alleviate with Redis?   
2.  CLUSTER command in PostgreSQL reorganized a table according to an index to improve performance, but:

1.  It rewrites the entire table under an exclusive lock, blocking any reads or writes
2.  PostgreSQL doesn't maintain the clustered layout for new data
3.  CLUSTER operation must ran periodically
4.  Only useful if you can take your databsse offline for long periods of time on a regular basis
5.  Critically, index-organized tables save space

1.  For tables with small rows that are mostly covered by the primary key, such as join tables, this can cut the table's storage footprint in half

1.  Major Version Upgrades can require downtime
2.  Somewhat cumbersome replication setup
3.   no-planner-hints dogma

1.  Can't mess with the query planner to use strategies it otherwise wouldn't use on its own

5.  No Block compression

1.  ZFS