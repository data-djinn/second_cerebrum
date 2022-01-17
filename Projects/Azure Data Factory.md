[[Azure (WIP)]][[Cloud]][[Data Engineering]][[Tech]]

# Azure Data Factory
- cloud-based data integration service that orchestrates the movement and transformation of data between various data stores and compute resources
- Azure Data Factorty (ADF) orchestrates data movement & transformation at scale
	- schedule data-driven workflows (called pipelines) that ingest data from disparate data stores
	- build complex ETL processes that transform data visually with data flows or by using compute services such as HDInsight, AZ Databricks, and synapse

## orchestratcion
- ![Data Factory platform](https://docs.microsoft.com/en-us/learn/wwl-data-ai/data-integration-azure-data-factory/media/hydrid-data-integration-scale.png)
- like an orchestra conductor, relying on different musicians (services) at different times to handle specific tasks


## ingest
### Extract
- define data source
	- resource group, subscription, and ID info such as key or secret
- define data
	- data to be extracted - DB query, set of files, or blob storage
### Transform
- operations can include splitting, combining, deriving, adding, removing, or pivoting
- map fields between data source & data destination
- may need to aggregate or merge
### Load
- define destination
	- JSON, file, or blob
	- write code to interact with application APIs
- start the job - first in dev/test env, then migrate to prod
- monitor the job - set up systems to provide info/notifications when things go wrong
	- set up logging

#### ADF has over 100 enterprise connectors 

## Benefits of ELT
- store data in its OG format
	- define the data structure during the transformation phase, so you can use source data in multiple downstream systems
	- reduces time required to load the data into destination system
	- also limits resource contention on the data sources
- ![Data Factory process](https://docs.microsoft.com/en-us/learn/wwl-data-ai/data-integration-azure-data-factory/media/data-driven-workflow.png)

# ADF Workflow
## Connect & collect
- define & connect all the required sources of data together, such as DBs, file shares, FTP web services
- ingest the data as needed too a centralized location for subsequent processing
## Transform & enrich
- Databricks/ML can be used to prepare or produce transformed data on a maintainable and controlled schedule to feed production environment with cleaned data 
## Publish
## Monitor
Azure Data Factory has built-in support for pipeline monitoring via Azure Monitor, API, PowerShell, Azure Monitor logs, and health panels on the Azure portal
![Data Factory Components](https://docs.microsoft.com/en-us/learn/wwl-data-ai/data-integration-azure-data-factory/media/data-factory-components.png)

1. connect linked service(s)
2. define data set
3. activities
	1. control flow is an orchestration of pipeline activities that includes chaining activities in a sequence, branching, defining parameters, and passing arguments
	- parameters are read-only key-value pairs defined in the pipeline


# ADF security
- to create adf instance, user must be a member of the *contributor* or the *owner* role, or an *admin* of the subscription
- to create & manage child resources, you must belong to *data factory contributo role*
- 