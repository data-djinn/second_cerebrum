[[Data Engineering]] [[Database Design]]

# Foundations of Data Systems

## Reliable, scalable, maintainable applications
- Most applications today are *data-intensive*, as opposed to *compute-intensive*
	- raw CPU power is rarely a limiting factor for these applications
	- bigger problems are usually:
		- the amount of data
		- complexity of data
		- speed at which it mutates
- applications need to:
	- store data so that they (or other apps) can find it again later (**databases**)
	- remember the result of an expensive operation, to speed up reads (**caches**)
	- allow users to search data by keyword or filter it in various ways (**search indexes**)
	- send a message to another process, to be handled asynchronously (**stream processing**)
	- periodically crunch a large amount of accumulated data (**batch processing**)
	- ![[Screenshot 2023-06-27 at 22.30.02.png]]

*3 most important concerns in software systems:*
### Reliability
==the system should continue to work correctly even in the face of adversity== (hardware or software faults, human errer)
Software working correctly:
- performs the function that the user expected
- it can tolerate the user making mistakes or using the software in unexpected ways
- its performance is good enough for the required use case, under the expected load & data volume
- the system prevents any unauthorized access & abuse
So, **reliability** means that the software continues to work correctly, even when things go wrong (*resilient*/*fault-tolerant*)
- in fault-tolerant systems, it can make sense to *increase* the rate of faults by triggering them deliberately, for example by randomly killing individual processes without warning
- many critical bugs are actually due to poor error handling - by inducing faults, you ensure the fault-tolerant design is continually tested

- hardware failures only cause 10-25% of outages - most of the rest are human coding errors
- how to design around human fallibility?:
	- minimize opportunities for error: 
		- well-designed abstractions & APIs
		- admin interfaces that make it easy to "do the right thing" & discourage "the wrong thing"
			- note: if they are too restrictive, people will work around them, thus negating their benefit
	- decouple places where people make the most mistakes from the places where they can cause failures
		- provide *fully featured non-production environments* where people can explore & experiment safely, using real data without affecting real users
	- **test thoroughly at all levels**, from unit tests to whole-system integration tests and manual tests
		- focus on covering corner cases that rarely arise in normal operation
	- allow quick & easy recovery from human errors, to minimize the impact in case of a failure
		- make it fast to roll back config changes
		- roll out new code gradually, so that unexpected bugs affect only a small subset of users
		- provide tools to recompute data (in case it's corrupted on incorrect)
	- set up **clear & detailed monitoring**, such as performance metrics & error rates (telemetry)
	- implement good training & management practices ((how??))


### Scalability
==as the system grows in data volume , traffic volume, or complexity, there should be reasonable ways of dealing with that growth==
#### Describing load 
- choose a small set of numbers to refer to as your *load parameters*
	- e.g.:
		- requests/second
		- read/write ratio in a database
		- simultaneously active users in a db
		- hit rate of a cache
	- perhaps you care about the average case, or want to pay attention to bottlenecks/outliers
#### Describing performance
- when you increase a load parameter and keep the system resources unchanged, how is the performance of your system affected?
- when you increase a load parameter, how much do you need to increase the resources if you want to keep performance unchanged?

- in a batch processing system such as hadoop, we usually care about *throughput* - the number of records we can process per second, or the total time it takes to run a job on a dataset of a certain size
- in online systems, we usually give more importance to *response time*

### Maintainability
==over time, many different people will work on the system - engineering and operations, both maintaining current behavior and adapting the system to new use cases. They should all be able to work on it productively==

We can design software in such a way that it will hopefully minimize pain during maintenance, and thus avoid creating legacy software ourselves:
- **Operability**
	- make it easy for operations teams to keep the system running smoothly
- **simplicity**
	- make it easy for new engineers to understand the system, by removing as much complexity from the system
- **Evolvability**
	- make it easy for engineers to make chang