[[DevOps]] [[Grafana]] [[[Loki]]

==Open-source, metrics-based monitoring system that collects data from services & hosts by sending HTTP requests on metric endpoints==
- stores the results in a time-series database & makes them available for analysis & reporting [[Grafana]]

# Why monitor?
- **enables alerts when things go wrong**, or preferably, warnings before they go wrong
- provides insight to enable analysis, bebugging, and resolution of the issue
- enables you to see trends/changes over time, helping in design decisions and capacity planning
	- e.g. how many active sessions at any given time

# 4 golden signals
## 1. **Latency**
- time it takes to serve a request - successful or failed
## 2. **Traffic**
- how much demand is being placed on your system
- e.g. for a web service, usually HTTP requests per second
## 3. **Errors**
- rates & specifics of failed processes
## 4. **Saturation**
- how full the services is
- latency increase is often an important indicator of saturation
- most systems degrade far before they achieve 100% utilization

# Prometheus metric types
## 1. Counter
- the numeric value of a counter always increases (unless reset to 0)
	- e.g. total HTTP requests recieved
	- number of exceptions/errors
- if a scrape fails, the counter only misses an increment

## 2. Guage
- **snapshot of a given point in time**
	- e.g. disk space, memory usage, etc
## 3. Histogram
- samples observations & counts them in configurable buckets
- used for things like request duration or response sizes
## 4. Summary
- provides a **total or aggregated count of observations**

# Prometheus components
![[Pasted image 20230117120100.png]]
## Server
- collects metrics, stores them in time-series DB, then makes them available for querying
- sends alerts based on metrics collected

## Scraping
- pull-based system: to fetch metrics, Prometheus sends an HTTP request (called a scrape) to targets
- targets can be statically defined or dynamically discovered
- each target is scraped at regular interval
- each scrape reads the /metrics HTTP endpoint to get the current state of the client metrics, & persists the values in the prometheus time-series database

## Client Libraries
- to monitor a service, you need to add instrumentation to your code
- there are client libraries available for all popular languages & runtimes (direct instrumentation)
- libraries let you define internal metrics & expose them to the HTTP endpoint

## Exporters
- for services you cannot add instrumentation to directly (e.g. Linux, Kafka, Nginx)
	- many applications expose metrics in non-prometheus format
- acts as a proxy between the application & Prometheus
- recieves requests from the Prometheus server, collects data, transforms them into prometheus format, & return them to prometheus server
- Popular exporters:
	- Windows
	- Node (for Linux)
	- Blackbox (for DNS)
	 - JMX for Java-based application metrics
- exporters are connected via static configuration, or dynamic service discovery can be used

## Alerting
- Pre-defined alerting rules send alerts to the Alertmanager
- Alertmanager then decides what to do with the alert:
	- ignore
	- silence
	- aggregate
	- send notification (email, slack, etc)
 