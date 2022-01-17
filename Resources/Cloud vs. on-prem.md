[[Cloud]]
# Intro
## On-prem vs cloud
### On-prem
#### computing environment
- physical servr
	- at least one OS, mulitple if using virtualization
- network infrastructure
- storage
- power
- cooling
- maintenance


#### Licensing
Typically paid per server or per client access license

#### Maintenance
- hardware
- firmware
- drivers
- BIOS
- OS
- software
- antivirus


#### Scalability
when you can no longer scale *up* a server, you must scale *out* (horizontally)
- must add another server node to a cluster
	- clustering uses a hardware or software load balancer to distribute incoming network requests to the node of the cluster
	- limitation of server clustering is that the hardware for each server in the cluster must be identical
		- therefore, when server reaches max capacity, all nodes must be upgraded or replaced

#### availability
3 9's (99.9%) --> 8.76 hrs downtime/year
4 9's (99.99%) --> .88hrs downtime/year
5 9's (99.999%) --> .09 hrs downtime/year

### Support
can be difficult to find server admins

### multilingual support
- can be an additional challenge, e.g. SQL Server must be set up with different collation setting
- greatly increases operational complexity


### TCO (Total Cost of Ownership)
- hardware
- software licensing
- labor (installation, upgrades, maintenance)
- datacenter overhead (power, telecom, building, heating, cooling)

difficult to align expenses with actual usage. orgs buy servers with extra capacity to accomodate for future growth

Because on-premises server systems are very expensive, costs are often _capitalized_. This means that on financial statements, costs are spread out across the expected lifetime of the server equipment. Capitalization restricts an IT manager's ability to buy upgraded server equipment during the expected lifetime of a server. This restriction limits the server system's ability to accommodate increased demand.


In cloud solutions, expenses are recorded on the financial statements each month. They're monthly expenses instead of capital expenses. Because subscriptions are a different kind of expense, the expected server lifetime doesn't limit the IT manager's ability to upgrade to meet an increase in demand.

## Cloud environment
### computing env
- pay-as-you-go
- reduced operational costs
- provision new virtualized "hardware" in minutes
	- reduced complexity & cost

### Maintenance
- cloud service provider manages a lot of this
	- physical hardware
	- networking
	- firewalls,
	- network security
	- datacenter fault tolerance
	- compliance,
	- physical security

### scalability
- measured in compute units
	- defined differently for each product/service
### availability
- SLAs ensure that customers know the capabilities of the platform they're using

### support
- environs are standardized

### multilingual support
- cloud systems often store data as a JSON file that includes the language code identifier
- apps that process data can use translation services to convert the data into an expected language when the data is consumed

### TCO
- cloud systems like az track costs by subscriptions
	- compute units, hours, or transactions
		- includes hardware, software, disk, and labor
	- economies of scale
	- orgs will be charged for a service that a CSP provisions but doesn't use (underutilization)
	- reduce these costs by ony provisioning instances after debs are ready to deply app to production [cloud] [azure] [best pracices]
		- use tools like emulators to develop & test cloud applications without incurring prod costs
	
	- 