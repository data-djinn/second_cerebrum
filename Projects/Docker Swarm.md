[[Docker]]


# Swarm 101
Two major components:
1. an enterprise-grade secure cluster
  - manage one or more Docker nodes as a cluster
  - consists of one or more nodes (first one will always be a manager)
  - encrypted distributed cluster store
  - encrypted networks
  - secure join tokens
2. an orchestration engine for creating microservices
  - API for deploying and managing microservices
  - define apps in a declarative manifest files
  - perform rolling updates, rollbacks, and scale apps

- a swarm consists of one or more docker nodes
- can be run on physical server, vms, cloud instances, even raspberry pi

**Manager nodes**: manage the state of the cluster, and dispatch tasks to the workers
**Worker nodes**: accept & execute tasks

- **Config & state is held in `/etcd/`** in the manager nodes
- Uses Transport Security Layer for:
  - encrypted communication
  - authenticate nodes
  - authorize roles
  - automatically rotate keys

- atomic unit of scheduling is a **swarm service**
- service is a construct that wraps the container, adding:
  - scaling
  - rolling updates
  - rollbacks
- a container wrapped in a service is a *task* or a a *task replica*

# Advantages of  using swarm
- minimal effort to set up clusters
- minimal learning curve for users & admins
- shortest path to production-ready cloud native deployments
- secure by default

## Good for:
- developer-lead organizations
- organizations with small operations teams
- teams that are newer to container orchestration
- teams that need to quickly deploy production-ready clusters at scale, but don't need the extensions & customization available in K8s
- 