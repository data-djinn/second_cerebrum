[[Data Engineering]] [[Cloud]]
pulled from [The Modern Data Stack: Open-source Edition (datafold.com)](https://www.datafold.com/blog/the-modern-data-stack-open-source-edition)

![[Pasted image 20211214160711.png]]

Before data can be used to inform decisions, it needs to go through these steps:

## 1. Specification:
#### defining what to track

## 2. Instrumentation:
#### registering events in your code
- industry standard: Segment
## 3. Collection:
#### ingesting & processing events
- industry standard: Fivetran
![[Pasted image 20211214162113.png]]
## 4. Integration:
#### adding data from various sources
- fivetran
## 5. Data Warehousing: 
#### storing, processing, and serving data
##### SQL ETL + BI
typical analytics requirements:
1. Current or anticipated large (terabyte-petabyte) and growing data volumn
2. many (10-100+) data consumers (people & systems)
3. load is distributed in bursts (e.g. heavy load in the morning, few at night)
4. query latency is important but not critical (5-30s is ok)
5. primary interface is SQL

**USE TRINO for SQL ETL + BI**
- feature-rich SQL interface
- works well for a wide range of use cases from ETL to BI tools
- Close to matching best-in-class SaaS offerings (Snowflake) in terms of usability & performance
- Provides a great trade-off between latency & scalability

**USE SPARK FOR ML & SPECIALIZED JOBS**
- not optimized for interactive query performance & usually has significantly higher latency for average BI benchmarks
- popular pattern is to have both Trino & Spark used in tandem for different types of workloads, while sharing data & metadata
![[Pasted image 20211214162934.png]]

**USE CLICKHOUSE/PINOT/DRUID FOR ULTRA-FAST SERVING**
- they combine storage + compute on a single node to minimize network traffic & thus reduce latency
![[Pasted image 20211214163225.png]]

## 6. Transformation:
#### preparing data for end users
##### SQL-centric workflows
**USE DBT**
- comprehensive yet lightweight: captures most use cases & it is easy to start with
- open-source yet smooth: on top of all benefits of OSS, it's easy to run
- Opinionated but reasonable: relies on common conventions & best practices
- Great documentation & community

##### Full featured data orchestration, including python
**USE AIRFLOW (standard), Prefect, or Dagster
- dagster has:
    - reuseable & composable tasks (called solids) - increasingly important as the complexity of data pipelines grows
    - data-aware tasks: dependencies between tasks can be verified both during development & in production
    - integrated testing, tracing, logging

## Streaming
apache spark is most obvious choice
- Flink is streaming-first of stateful streaming transformations (e.g. to calculate metrics over user sessions)
- Low latency (millisecond scale vs. second with Spark)
## 7. Quality assurance:
#### bad data = bad decisions
- great_expectations seems to be the only choice here...
## 8. Data discovery: 
#### finding the right data asset for the problem
- where do i find the data/prior analytical work on a topic?
- can I trust that data? who else uses it? when was it last updated? are there any known quality issues?
**use Amundsen**
## 9. Analysis: 
#### creating the narrative
**metabase** for self-service
**lightdash** looks awesome - open-source Looker
- enables large number of users in an org to explore, analyze, and dashboard data without writing code, and therefore help them avoid the pitfalls of incorrectly using data
**Benefits**
- Productivity
- Data quality: preserves SSOT by keeping your business logic DRY, facilitates reliable change management with version control
- Scalability: by managing business logic in a structured way, the BI tool can scale to thousands of data users while providing transparent and effective access control, minimize load on the data warehouse with smart caching, and reduce the amount of duplicate content created & shared