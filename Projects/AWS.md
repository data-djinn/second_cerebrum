[[Cloud]]
### cloud:
- $400b+ industry
- 50%+ of all corporatate data stored in cloud (10^22 bytes)
- massive YoY growth - +18.4% 2020-2021
- huge technical advantages for advanced machine learning (e.g. deep learning)
  - more data storage 
  - more computational power
  - more reliability (training ML algos can take days - reliability is important)
- reduced time to production


# AWS 
- offers over 200 services
- data centers all over the world:
  - **Region**: physical location around the world where we cluster data centers (aws.amazon.com)
  - **availability zone**: one or more discrete data centers with redundant power, networking, and connectivity in an AWS region

# AWS cloud value framework
## Cost savings (TCO)
- infrastructure cost savings/avoidance from moving to the cloud
  - total cost of owning and operating servers and data centers
  **moving to the cloud introduces a new cost structure**, including:
    - compute
    - storage
    - networking
  **while simultaneously eliminating on-prem related costs**, such as:
    - space
    - power
    - cooling
    - physical security
Takes advantage of *economy of scale*, and reduce CapEx

#### Opitimizing costs:
- leverage the **pay as you go** model
  - allocate the necessary resources only
  - deallocate resources when they become unnecessary
- leverage the different pricing plans
  - adapted to the your use case
  - e.g. renting VMs for long contracts
- **use managed services**
  - e.g. using amazon aurora for relational dbs is 10x less costly than running on-prem alternative
#### Tools:
- aws cost explorer
- aws cost and usage report
- aws budgets

studies showed average IT infrastructure spend per user reduced by 27%, decreases to 42% less for mature customers at large scale

## Staff Productivity
- efficiency improvements by function on a task-by-task basis
## Operational Resilience
- benefit of improved availability, security, and compliance
## Business Agility
- deploying new features/applications faster and reducing errors


# AWS Core Services
## compute (EC2 VMs)
- provided security layer
- resizable capacity
#### several pricing plans
  - on-demand
  - saving plans (usage commitment for 1 or 3 years -> cost savings)
  - reserved instances 
  - spot instances
    - leverage AWS unused capacity as it comes up, save 90%!
    - only to be used for fault-tolerant, stateless applications
  - dedicated hosts
#### benefits
- flexibility - allocate & deallocate capacity at will
- scalability - use other services to spin up & down to eliminate unused capacity
- reliability
- security
- competitive rates 
- **rich catalog of machines to customize** (key advantage over lambda):
    - memory
    - processor
    - storage
    - OS

### Serverless compute (lambda)
- run code without managing servers / VMs
- **Elastic**: automatically adapts to workloads
- supports several programming langs:
    - python
    - node.js
    - java
- can be triggered from AWS services or external applications
### Benefits of lambda
- transfer server management to AWS 
- continuously scaling to the size of workloads:
    - increases reliability by eliminating traffic-related downtime
    - decreases costs by allocating just the necessary resources to handle current traffic
- **pricing: time of code execution with millisecond metering**
- rich ecosystem of application models & repos

## Database
### S3 (simple storage service)
- data stored as **objects**: similar to traditional files but stored differently
  - data lakes
  - big data analysis
  - archives
  - websites
##### S3 Glacier
- well suited for **long term storage**, e.g.
    - backup
    - archives
- several retrieval delay options - longer = cheaper:
    - expedited retrievals: 1-5 minutes
    - standard retrievals: 3-5 hours
    - bulk retrievals: 5-12 hours
- S3 Glacier Deep Archive: 12-48 hours

#### Benefits
- data availability - automatically replicated in several servers and sites
- performance & scalability - read/write operations have low latency, even with significant amounts of data
- offers 99.99999999% durability - with 10M objects on S3, one can expect to lose one object every 10,000 years
- no storage capacity limit
- security
- compliance
- highly competitive pricing
- optimized pricing for long-term storage with s3 glacier

### RDS 
- managed RDB
- Several database engines available:
  - amazon aurora
  - postgres
  - mysql
  - mariaDB
  - oracle db
  - sql server

#### benefits
- several time-consuming db management tasks handled by AWS:
  - DB config
  - updates & security patches
  - backup
- easy adaptation to traffic evolutions (just a few clicks!)
- availability & durability (multiple availability zones)
- high performance
- security
- cost savings

### DynamoDB
- noSQL database:
  - key-value DB
  - documents
- fully managed by AWS:
    - Security
    - backup
    - restore
    - caching
- well suited for **Big data**

#### Benefits
same as RDS

## management & governance
## Networking & content delivery
## security, identity, & compliance
## Storage

# AWS well-architected Framework