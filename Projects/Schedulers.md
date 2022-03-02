[article](https://copyconstruct.medium.com/schedulers-kubernetes-and-nomad-b0f2e14a896)
- schedulers may not be necessary at smaller firms/projects
- except that it makes it easier to repeatedly and reliably deploy your software

## A scheduler:
1. hides the details of resource management and failure handling so its users can focus on application development instead  
2. operates with very high reliability and availability, and supports applications that do the same  
3. lets us run workloads across tens of thousands of machines effectively.

achieves high utilization by combining:
- admission control
- efficient task-packing
- over-commitment
- machine sharing with process-level performance isolation

It supports high-availability applications with runtime features that minimize fault-recovery time, and scheduling policies that reduce the probability of correlated failures. Borg simplifies life for its users by offering a declarative job specification language, name service integration, real-time job monitoring, and tools to analyze and simulate system behavior.

