[[Azure]] [[Cloud]]  [[Data Engineering]]

### All solutions:

-   Durable and highly available
-   Secure
-   Highly scalable
-   Microsoft managed
-   Accessible across the globe

  
 

## 4 different solutions

-   AZ Storage blobs

-   Hierarchical name space (like file explorer)
-   Create containers as groupings to dump blobs in

-   AZ Data Lake Store

-   Enterprise-wide, hyperscale repository
-   Store data in its native format, store multiple formats
-   Source system --> data lake --> data factory (ELT)

-   AZ Cosmos DM

-   Non-relational
-   GLOBAL
-   Multi-model
- HBase on HDInsight

## Key selection criteria:

-   How large is the dataset?
-   What are the performance requirements? Horizontal or vertical scaling? How with the data move? (in/out)
-   What types of data will be ingested?
-   Where will the data be consumed? (backend/frontend)
-   How will the data be accessed?
-   Are redundancy and availability a concern?
[Azure Data Architecture Guide - Azure Architecture Center | Microsoft Docs](https://docs.microsoft.com/en-us/azure/architecture/data-guide/)
 [Choosing a data storage technology - Azure Architecture Center | Microsoft Docs](https://docs.microsoft.com/en-us/azure/architecture/data-guide/technology-choices/data-storage)

Capability	Azure Data Lake Store	Azure Blob Storage container
Purpose	Optimized storage for big data analytics workloads	General purpose object store for a wide variety of storage scenarios
Use cases	Batch, streaming analytics, and machine learning data such as log files, IoT data, click streams, large datasets	any type of text or binary data, such as application back end, backup data, media storage for streaming, and general purpose data
Structure	Hierarchical file system	object store with flat namespace
Authentication	based on Azure AD	based on shared secrets account acces keys, and shared access signature keys, and RBAC
Authentication protocol	Oauth 2.0 calls must contain valid JSON web token issued by Azure AD	hash-based message auth code. Calls must contain base64 SHA-256 hash over a part of the HTTP request
Authorization	POSIX access control lists	Access keys for account level auth, otherwise Shared Access Signature keys
Auditing	available	available
Encryption at rest	transparent, server side	transparent, server side; client-side encryption
Analytics workload performance	optimized performance for parallel analytics workloads, high throughput and IOPS	not optimized for analytics workloads
size limits	No limits on account sizes, file sizes, or number of files	specific limits documented
Geo-redundancy	Locally-redundant (LRS), GRS, read access GRS, ZRS	LRS, GRS, RA-GRA, ZRS

  File storage capabilities   
Purpose   
Structure   
Authentication   
Authentication   
Authorization   
Auditing   
Erxryption at   
Developer   
SDKs   
Analytics   
workload   
performame   
Size limits   
redundancy   
Aure Lake Store   
Optimized storage for big data analytics   
workloads   
Batch, streaming analytics, and machine   
learning data such as log files, IOT data,   
click streams, large datasets   
Hierarchical fie system   
Based on Azure Active Directory   
Identities   
OAuth 2.0. Calls must contain a valid   
JWT (JSON web token) issued by Azure   
Active Directory   
POSIX access control lists (ACLs). ACLs   
based on Azure Active Directory   
identities can be set file and folder level.   
Available.   
Transparent, server side   
.NET, Java, Python, Node.js   
Optimized performance for parallel   
analytics workloads, High Throughput   
and ops   
No limits on account sizes, file sizes or   
number of files   
Locally-redundant CRS), globally   
redundant (GRS), read-access globally   
redundant (RA-GRS), zone-redundant   
Aure Blob containa•s   
General purpose object store for a wide variety of   
storage scenarios   
Any type of text or binary data, such as application   
back end, backup data, media storage for   
streaming, and general purpose data   
Object store with fiat namespace   
Based on shared secrets Account Access Keys and   
Shared Access Signature Keys, and role-based   
access control (RBAC)   
Hash-based message authentication code (H MAC).   
Calls must contain a Base64-encoded SHA-256   
hash over a part of the HTTP request.   
For account-level authorization use Account Access   
. For account, container, or blob authorization   
use Shared Access Signature Keys   
Available   
Transparent, server side; Client-side encryption   
.NET, Java, Python, Node.js, C++, Ruby   
Not optimized for analytics workloads   
Specific limits documented here   
Locally redundant (IRS), globally redundant (GRS),   
read-access globally redundant (RA-GRS), zone-   
redundant (ZRS). See here for more information
[NoSQL database capabilities 
Primary 
database 
model 
indexes 
SQL language 
support 
Consistency 
Native Azure 
Functions 
integration 
global 
distnt)uüon 
Priciry model 
Aure Cosmos DB 
Document store, graph, kervalue store, wide 
column store 
Strong, bounded-staleness, session, consistent 
prefiK eventual 
Elastically scalable request units (ROS) charged 
per-second as needed, elastically scalable 
storage 
HB%e HDlMÉht 
Wide column store 
Yes (using the Phoenix 
JDBC driver) 
Strong 
No HBase cluster replication can be 
configured across regions with eventual 
consistency 
Per-minute pricing for HDlnsight cluster 
(horizontal scaling of nodes), storage ]

![Analytical database capabilities 
Primary database model 
SQL language support 
Priciry model 
Authentication 
Eruyption at rest 
Analytics workload performance 
Size limits 
Aure 
Relational (column store), telemetry, and time series store 
Elastically scalable cluster instances 
Based on Azure Active Directory &ntities 
Supported customer managed keys 
Optimized performance for parallel analytics wærkloads 
Linearly scalable ]