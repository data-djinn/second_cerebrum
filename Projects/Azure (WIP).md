[[Azure]][[Cloud]]
# Introduction
 ### Public cloud
 - no capital expenditures to scale up
 - applications can be quickly provisioned and deprovisioned
 - organizations pay only for what they use
 
 ### Private cloud
 - organizations have complete control over resources
 - organizations have complete control over security

### Hybrid cloud
- most flexibility
- organizations determine where to run their applications
- organizations control security, compliance, or legal requirements   

# Compute
##   CPU & Memory
###  Virtual Machines
-  Linux
-   Windows
-   VM Images
-   Scale sets
	-   Identify a VM image, & automatically scale based on load (up & down)
-   Availability sets
-   Managed Disks (microsoft manages storage & back end)

###  App Services
-   Web app framework choice (node.js, java, PHP)
-   Flexible deployments
-   Scaling
-   Healing
-   Deployment slots

###   Containers
-   Azure Kubernetes Service (AKS)
-   Container instances
-   Azure container registry (catalog)

###  Serverless
-   Azure functions (write code in azure, triggered by external stimulus i.e. event)
-   Logic apps

###  Overlapping Solutions
-   Serverless container instances
-   Service fabric ties together
	-   Containers, executables, webapps
	-   Mix Linux & windows containers
-   Compute at scale

#### Azure batch service
-   Schedule jobs at scale

#### HDInsight
-   Process big data

# Store & Process Data
Our solution needs to be:
-   Scalable
-   Available
-   Global

## Database Storage Services
### Self managed
-   VMs or Containers
	-   May be a predifined image
	-   You manage the compute & disks
	-   Patching is your responsibility

###  Service-based
-   Provision an instance
-   Choose scale characteristics
-   Managed by Azure
-   No patching required

## Relational Databases
### Azure SQL DB
-   "Sql server in the cloud"
-   Managed instance, easy migration

### MySQL
### Maria DB
### PostgreSQL

### Other data storage options:
#### Table storage
-   Key value storage
	-   Unique keys to identify a set of data
	-   & a set of properties that go along

####   Blob storage
-   For large files
-   PDFs, virtual hard-drives

#### Azure Queus
-   Short term storage, messages that you need to pass between nodes

#### Redis Cache
-   Take the load of back-end storage
-   Save on costs & improve performance in some cases

#### MongoDB, Cassandra, Neo4j can also be used as self-service

####  Azure CosmosDB
-   Unique db offering
-   Multi-model storage offering
		-   Graph/gremlin API
		-   Table
		-   Cassandra
		-   Document (MongoDB / SQL)
-   Globally distributed
		-   Multi-master
		-   Low latency
		-   Five consistency levels
### Azure Data Lakes v2
-   Large scale data storage built for analytics
-   Multi-modal access - File/Blb
-   Built on Azure Blob Storage

## Data Processing
-   Ingestion Event Hubs
-   Ingest / Process messages & data & put into data lakes, blob storage
-   Built to scale
-   Can handle telemetry data (high volume)
-   Moving ETL / Data Factory
-   Take data from different systems & clouds
-   Load, tranform, process into other systems

## Analyze data
-   Stream analytics
-   Real time data analysis
-   High volume message processing
-   Ingest, analyze, output

-   Azure HD Insight - create clusters from:
	-   Spark
	-   Hadoop
	-   Hive
	-   Storm
	-   Kafka
	-   Hbase
	-   Azure Data Bricks
	-   Cloud-optimized Spark service
	-   Deep Azure integration

## Machine Learning and Visualizing Data
-   Starting points for all levels
-   Create ML model quckly with guidance & assistance for what algorithm to use
-   Build and train models
-   Deploy & manage within ML system with a Devops experience

### Cognitive Services - prepackaged ML
-   Decisions
-   Speech
-   Language
-   Vision
-   Search

# Integrating Applications in Azure

## Integration = connecting systems & applications

### Cloud integrations
-   Within a cloud
-   Cloud to data center
-   Between clouds
-   Cloud to SaaS

### Messaging and Events
-   Azure Service Bus
    -   Built for brokered or relayed messaging
    -   Brokered - message gets sent to service bus to get cued up for sending to recipient client
    -   Relayed - nodes talking to each other through service bus
    -
-   Event gird - publish subscribe event notification
    -   Tells you something happened, allowing you to process
    -   API Management - publishing, securing, and managing of APIs
    -   Create developer portals, composite API

-   Logic Apps - workflow
    -   Provides orchestrating messaging interactions
    -   Control flow - what your process looks like between different messages

-   Integration Account
==A container for schemas, maps, and trading partners that extends logic apps==
    -   Specific features you need with integration e.g.
    -   Enterprise file formats (.csv, .edi)
    -   XML/JSON transformations
    -   Partner management
        -   Integration is about connecting systems
    -   Messaging and workflow are crucial
    -   Integration accounts extend capabilities

# Networking in the Azure

## Networking in the cloud

## Virtual Networks

Allow you to define a network

Many related things you can set up, i.e. public IP address, network security groups

Service Endpoint Policies give you more control about what can access your data (only from internal virtual networks)

## VPN

Express route

On-premise gateway

## Networking Services

CDN - content delivery network

Allows you to cache data "on the ede"

Traffic Manager

Write rules & manage the traffic for customers for load balancing as an example

Load Balancer

DNS Zones - name resolution, don't deal with IP addresses

### Edge service

### DDOS protection

Application gateway - set up front end for SSL offloading

Front door service - creates entry points into microservice architecture (for security, load, & traffic)

## In Summary
-   Networking connects resources
	-   Gives you the power to control how data flows
	-   Robust networking options around caching, securing, routing data:
		-   Between Azure resources
		-   Between Azure & external resources

# Managing & Monitoring Azure Resources

## Subcriptions & Accounts
-   Create management groups (containers) under the main account (root management group)