[[Cloud]][[Azure (WIP)]][[Tech]][[Data Engineering]][[Security]]
## Encryption at rest
- all data written to Azure storage is automatically encrypted by Storage Service Encryption (SSE) with a 257-bit cipher
	- FIPS 	140-2 compliant
- SSE automatically encrypts data during write
- encrypt virtual hard disks  using AZ Disk Encryption
	- uses bitlocker for Windows images
	- dm-crypt for Linux
- AZ Key Vault stores the keys automatically to help you control and manage the disk-encryption keys and secrets

## Encryption in transit
- *always* use HTTPS to secure comms over public internet
- calling REST APIs to access objects in storage accounts, ***enforce use of HTTPS*** by requiring [secure transfer](https://docs.microsoft.com/en-us/azure/storage/storage-require-secure-transfer)
	- refuses all HTTP connections
	- requires SMB 3.0 for all file share mounts


### CORS support
- AZ storage supports cross-domain access through cross-origin resource sharing (CORS)
- using CORS, web apps ensure that they load only authorized content from authorized sources
- this is an *optional flag* you can enable on storage accounts

### RBAC (Role-based access control)
- to access data in a storage account, the client makes a request over HTTP/HTTPS
	- every request to a secure resource must be authorized
		- ensures that the client has the permissions required to access the data
- AZ storage supports AZ Active Directory and RBAC for both resource management & data operations
- for security principals, you can *assign RBAC roles that are scoped to the storage account*
	- use Active Directory to authorize resourse management ops, i.e. config
	- AD is supported for ops on blob & queue storage
- assign RBAC roles scoped to a subscription, resource group, storage acct, or individual container/queue

### Auditing access
- auditing is another part of controlling access
- audit via built-in storage analytics service
	- logs every op in real time
	- search the storage analytics logs for specific requests
	- filter based on the auth mech, the success of the operation, or resource accessed


# Storage Acct keys
- accounts can create authorized apps in AD to control access to the data in blobs and queues
	- this auth approach is the best solution for apps that use blob storage 
- clients can use a **shared key** (shared secret)
	- auth option is the easiest to use, & supports:
		- blobs
		- files
		- queues
		- tables
	- client embeds the shared key in HTTP authorization header of every request, & storage validates the key
- an application can issue a GET request against a blob resource
	- GET http://myaccount.blob.core.windows.net/?restype=service&comp=stats
`x-ms-version: 2018-03-28  
Date: Wed, 23 Oct 2018 21:00:44 GMT  
Authorization: SharedKey myaccount:CY1OP3O3jGFpYFbTCBimLn0Xov0vt0khH/E5Gy0fXvg=`


## storage account keys
- shared keys are called *storage account keys* 
- az creates 2 of these keys (primary & secondary) for each storage account you create
	- these keys gibe access to *everything* in the account

## Protect shared keys 
- only 2 keys in the whole account, and they provide access to *the whole account*
	- use only with trusted in-house applications that you control completely
- if the keys are compromised, change the key values in the AZ portal


Here are several reasons to regenerate your storage account keys:

-   For security reasons, you might regenerate keys periodically.
-   If someone hacks into an application and gets the key that was hard-coded or saved in a configuration file, regenerate the key. The compromised key can give the hacker full access to your storage account.
-   If your team is using a Storage Explorer application that keeps the storage account key, and one of the team members leaves, regenerate the key. Otherwise, the application will continue to work, giving the former team member access to your storage account.


# Understand shared access signatures
- don't share storage account keys with 3rd party apps
	- use *shared access signature* (SAS) instead
		- ==string that contains a security token that can be attached to a URI==
		- use a SAS to delegate access to storage objects and specify constraints, such as the permissions and the** time range of access**
	- you can give a customer a SAS token, for example, so they can upload pictures to a file system in Blob storage
		- seperatley, you can give a web app permission to read those pictures

## types of shared access signatures
- use *service-level* SAS to allow access to specific resources in a storage account
	- e.g. allow an app to retrieve a list of files in a file system, or to download said files
- use *account-level* SAS to allow access to anything that a service-level SAS can allow, plus additional resources and abilities
- **USE SAS FOR A SERVICE WHERE USERS READ & WRITE THEIR DATA TO YOUR STORAGE ACOUNT**
#### 2 designs:
- clients upload & download data through a front-end proxy service
	- proxy performs authentication & can validate business rules
	- if the service must handle large amounts of data or high-volume transactions, this may be too complex / complicated
	- ![A client-side diagram.](https://docs.microsoft.com/en-us/learn/data-ai-cert/secure-azure-storage-account/media/4-client-flowchart.png)
- lightweight service authenticates the client, as needed
	- then generates a SAS
	- after recieving the SAS, client can access storage account resources directly
		- SAS defines the cline's permissions & access interval
		- reduces need to route all data through the front-end proxy service


# Control network access to storage acct
- *by default, storage accounts accept connections from clients on **any network***
- to limit access to select networks, you must first change the default action
	- you can restrict access to specific IP addresses, ranges, or virtual networks

# Advanced thread protection for AZ storage
- extra layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit storage accounts
	- address threats without being a sec expert or managing security monitoring systems
- security alerts are triggered when anomalies in activity occur
- alerts are integrated w/ az security center, and are also sent via email to subscription admins, with details of suspicious activity/recommendations on how to investigate and remediate threats
- az defender for storage is currently available for **blob storage, az files, and az data lake gen2**
- available in all public & US Gov clouds

# Data lake storage security features
- Gen2 enables enterprises to consolidate their data
- built on az blob storage, so inherits 
- along with RBAC, Data Lake Storage Gen2 provides access control lists (ACLs) that are POSIX-compliant, restricting access to only authorized users, groups, or service principals
- restrictions are flexible, granular, and manageable
- authethicates via AZ AD OAuth 2.0
	- federation with AZ AD connect & MFA
- integrated into main anatlytics services that use the data
	- e.g. AZ DataBricks, HDInsight, AZ Storage Explorer and AZ Synapse Analytics
- this on top of end-to-end encryption of data + transport layer protections completes the shield of security