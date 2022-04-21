[[Docker]] [[Projects/DevOps]]
## Microservices
- split the application into a series of small, independent services
- can be **built**, **modified**, and **scaled** separately, with relatively little impact on one another
- containers excel when it comes to managing a large number of small, independent workloads
    - containers and orchestration make it easier to manage & automate the process of deploying, scaling, and connecting lots of microservice instances

# Cloud Transformation
- Containers help you move to the cloud
    - it is easy to wrap existing software in containers
    - since containers use fewer resources than VMs, you can save money on cloud resources

# Automated scaling
- Containers are small & can start up quickly
- If the system detects an increase in usage, it can spin up new containers in a few seconds
- this **increases stability** & **reduces cost**

# Continuous Deployment
- containers make it easy to test code in an environment that is the same as production, because the code can be automatically tested inside the container itself
- an automation pipeline for CD can automatically build a container image with the new code, test it, then automatically ship that same container image production
- because the production environment (container) is built right into this automated process, developers even have the ability to use it for testing & troubleshooting

# Self-healing applications
==applications that automatically detect when something is broken & automatically take steps to correct the problem without the need for human involvement==
- containers are easy to restart - destroy & replace in a few seconds

# Developer visibility
- in more traditional environments, it can be difficult for everyone to get access to a production system to troubleshoot when something goes wrong
    - leads to "it works on my machine!"
- **Container *is* the production environment**
    - anyone can spin up an environment that is exactly like production, even on their own laptop
    - developers (and others) have the ability to test & see exactly how it will behave in production
    - the additional *visibility* offered by containers can help your organization develop & troubleshoot