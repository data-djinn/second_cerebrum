# Motivations & Use Cases
### event-driven architecture
- from a static snapshot -> continuous stream of events
- single platform to connect everyone to every event
- real-time stream of events
- all events stored for historical view

# Fundamentals
#### Producers
- application that writes events to kafka cluster
- 
# Why is kafka fast?
- kafka is optimized for fast throughput - moves a large amount of data in a short time (efficiently)
	- like a large pipe moving liquid
	- the bigger the diameter of the pipe, the larger the volume of data that can move through quickly
### Reliance on sequential I/O
- I/O to disk is thought to be slow
	- this is true for random access patterns
	- for hard drives, it takes time to move the arm to different locations on the magnetic disks
		- this is what makes random access slow
- for sequential access, it is much faster to read & write blocks of data one after the other
	- Kafka takes advantage of this by using an append-only log as its primary data structure
	- add new data to the end of the log
![[Pasted image 20240731210033.png]]
- hard disks are 1/3 of the price with 3x the capacity compared to SSDs
- this means that kafka can *cost effectively* retain messages for a long period of time

### Zero-copy principle
- it's critically important to eliminate excess copy when moving pages & pages of data
- modern unix systems are optimized to transfer data from disk to network without excess copy
#### typical access pattern when zero-copy is not used
![[Pasted image 20240731210417.png]]
### Zero copy 

![[Pasted image 20240731210619.png]]