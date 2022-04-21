[[Projects/DevOps]][[Terraform]][[Docker]]

## Traditional architecture pattern
- monolithic architecture with multiple components
- deployed as a single unit
- **Problem**: if there's a bug in the e.g. login system, we can't just change a single component, we have to reconfigure all components and redeploy as a single application
- **Solution**: 
## microservice/service-oriented architecture
  - deliver each component as a discrete application
    - now you can patch e.g. login service of component A without coordinating with components B, C, & D
- this buys us **development agility** - each team can deploy at whatever pace they want
- this also introduces 
#### operational challenges:
##### Discovery: how do the different pieces discover one another
- monolithic architecture on a VM can call other components in-memory, like a function call
  - nanoseconds
- microservice architecture calls over a network, slowing calls to milliseconds
- historically, each service tier gets their own load balancer & each process calls the required service's load balancer IP address
  - load balancer routes call to available instance of the service
  - proliferation of load balancers quickly becomes complex/unmanagable 
  - *load balancer becomes single point of failure*
  - load balancer as middle man adds even more latency
- **Consul solves this by providing a central service registry**
  - when each instance boots, it is populated in the register
  - when services need to call other services, it queries the register
  - when an instance dies, the register picks that up and will not refer it to calling service
##### Configuration
- monolithic app might use a single XML/YAML file to 

##### Segmentation