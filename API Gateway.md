#system-design 

- amazon api gateway
- azure api management
- google api gateway
- cloudflare
### what is an API gateway?
- single point of entry to the clients of an application
- sits between client & collection of backend services
### functions
- authentication & security policy enforcements
- load balancing & circuit breaking
- protocol translation & service discovery
- monitoring, logging, analytics, and billing
- caching

### Workflow
1. client sends request to API gateway
	- typically http-based
		- graphQL, REST
2. API gateway validates the HTTP request
	- parameter validation
3. Check the caller's IP address & other HTTP headers against allow/deny list
	- can also perform basic rate limit checks against attributes:
		- IP address
		- HTTP headers
4. API gateway passes the request to an identity provider for authentication & authorization
	- receives an authenticated session back from the provider with the scope of what the HTTP request is allowed to do
5. higher-level rate-limit check is applied against the authenticated session
	- if it's over the limit, request is rejected
6. Dynamic routing
	- locates the approriate service to transform 
1. service discovery