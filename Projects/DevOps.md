# What is DevOps?
##### DevOps = Development + operations
==DevOps is a software engineering *culture* and *practice* that aims at unifying software development & software operations. DevOps aims at shorter development cycles, increased deployment frequency, more dependable releases, in close alignment with business objectives==

- first & foremost a grassroots culture of *collaboration* 
    - Development & Operations traditionally had fundamentally different goals:
        - developers wanted speed to delivery
            - would often just "throw code over the wall"
        - operators wanted stability
            - every change to the system introduces a stability risk
    
    - Dev & Ops teams should work *together*, be measured by the same metrics, scoring both speed & stability together
        - dev & ops teams work *together* to create & use tools and processes that support both speed and stability
 
### Goals:
- faster time-to-market
- fewer production failures
- immediate recovery from failures

- This culture has given rise to a set of practices:
## Build automation
==automation of the process of preparing code for deployment to a live environment==
- depending on what languages are used, code needs to be compiled, linted, minified, transformed, unit tested, etc.
- build automation means taking these steps & doing them in a consistent, automated way using a script or tool
- the tools of build automation often differ depending on what programming languages and framework are used, but they're all **automated**
- usually, build automation looks like running a command-line tool that builds code **using configuration files and/or scripts that are treated as part of the source code**
- independent of an IDE - should work the same way outside the IDE
- as much as possible, build automation should be agnostic of the configuration of the machine that it is built on

### Why do build automation?
- **fast** - automation handles tasks that would otherwise need to be done manually
- **consistent** - the build happens every time, removing problems and confusion that can happen with manual builds
- **repeatable** - the build can be done multiple times with the same result. any version of the source code can always be transformed into deployable code in a consistent way
- **portable** - the build can be done the same way on any machine - anyone can build on their machine, as well as on any shared build server. Building code doesn't depend on specific people or machines
- **reliable** - there will be fewer problems caused by bad manual builds

## Continuous Integration (CI)
==frequently merging code changes done by developers==
- CI means merging constantly *throughout the day*, usually with the execution of *automated unit tests* to detect any problems caused by the merge
- usually done with the help of a **CI server**
    - server sees the change & automatically performs a build, executing automated tests
    - this occurs multiple times a day

### Why do continuous integration?
- **early detection of bugs** - if code doesn't compile or an automated test fails, the developers are notified and can fix it immediately
    - the sooner bugs are detected, the easier they are to fix
- **Eliminate the scramble to integrate before release** - since code is constantly merged, there is no need to do a big merge at the end of the release cycle
- **makes frequent releases (CD) possible** - code is always in a state that can be deployed to production
- **makes continuous testing possible** - since the code can always be run, QA testers can get their hands on it all throughout the development process, not just at the end
- **encourages good coding practices** - frequent commits encourage simple, modular code

## Continuous Delivery/Deployment (CD)
==continuously maintaining code in a deployable state==
- regardless of whether or not the decision is made to deploy, the code is always in a state that is able to be deployed
- continuous delivery is keeping the code in a deployable state, whereas continuous delivery is actually doing the deployment frequently
- the more often you deploy, the better! not a big, scary event
- each version of the code goes through series of stages - automated build & test, manual acceptance testing
- when decision is made to deploy, the **deployment is automated**
- if deployment causes a problem, it is quickly & reliably **rolled-back**

### Why do CD?
- **faster time-to-market** - get features into the hands of customers more quickly, rather than waiting for a lengthy deployment process that doesn't happen often
- **Fewer problems caused by the deployment process** - since deployment is frequently used, any new problems are more easily discovered
- **Lower risk** - less changes deployed at once means less risk
- **reliable rollbacks** - robust automation means rollbacks are a reliable way to ensure stability for customers, and rollbacks are not painful for developers
- **Fearless deployments** - everyday event, not big scary event

## Infrastructure as Code
==manage and provision infrastructure through code & automation==
- use IaaC to create & change:
    - servers
    - VM instances
    - environments
    - containers
    - other infrastructure
- without IaaC, you might:
    -  SSH into a host
    -  issue a series of commands to perform the change
-  with IaaC:
    -  Change some code or configuration files that can be used with an automation tool to perform changes 
    -  commit them to source control
    -  use an automated tool to enact the changes defined in the code &/|| config files
-  provisioning new resources & changing existing resources are both done through automation

### Why do IaaC?
- **Consistently in creation & management of resources** - the same automation will run the same way every time
- **Reusability** - code can be reused to make the same changes consistently across multiple hosts & can be used again in the future
- Scalability - need a new instance? you have one configured exactly the same way as the existing instances in minutes (or seconds)
- **Self-documenting** - changes to infrastructure document themselves to a degree. The way a server is configured can be viewed in source control, rather than being a matter of who logged in to the server and did something
- **simplify the complexity** - complex infrastructure can be stood up quickly once they are defined as code. a group of several interdependent servers can be provisioned on demand


## Configuration Management
==maintaining & changing the state of pieces of infrastructure in a consistent, maintainable, and stable way==
- changes always need to happen - configuration management is about doing them in a maintainable way
- configuration management helps us to minimize *configuration drift*: the small changes that accumulate over time and makes systems different from one another & therefore harder to manage
- IaaC is very beneficial for config management
- Config management is about managing configuration somewhere *outside of the servers themselves*

### why do config management?
- **save time** - less time to change config
- **insight** - good configuration management tells you about the state of all pieces of a large and complex infrastructure
- **maintainability** - a more maintainable infrastructure is easier to change in a stable way
- **Less configuration drift** - it is easier to keep a standard configuration across a multitude of hosts

## Orchestration
==automation that supports processes & workflows, such as provisioning resources==
- managing complex infrastructure shold be less like being a builder and more like conducting an orchestra
- instead of going out and creating a piece of infrastructure, the conductor simply signals what needs to be done and the orchestra performs it
    - conductor does not need to control every detail
    - musicians (automation) are able to perform their piece with only a little bit of guidance

### Why do orchestration?
- **Scalability** - resources can be quickly increased or decreased to meet changing needs
- **stability** - automation tools can automatically respond to fix problems before users see them
- **save time** - certain tasks and workflows can be automated, freeing up engineers' time
- **self-service** - orchestration can be used to offer resources to customers in a self-service fashion
- **granular insight into resource usage** - orchestration tools give greater insight into how many resources are being used by what software, services, or customers

## Monitoring
==the collection & presentation of data about the performance and stability of services and infrastructure==
- Monitoring tools collect data over things such as:
    - usage of memory
    - cpu
    - disk/io
    - other resources over time
    - application logs
    - network traffic
- the collected data is presented in various forms, such as charts & graphs, or in the form of real-time notifications (*or response*) about problems
- Real-time notifications:
    - monitoring tool detects if performance on the website is beginning to slow down & immediately notifies an administrator, who intervenes before downtime occurs

### Why do monitoring?
- **fast recovery** - the sooner a problem is detected, the sooner it can be fixed
- **better root cause analysis** - the more data you have about your production services, the easier it is to determine the root cause of a problem
- **visibility across teams** - good monitoring tools give us useful data about the performance of code across production
- **automated response** - monitoring data can be used alongside orchestration to provide automated responses to events, such as automated recovery from failures

## Microservices
==a microservice architecture breaks an application up into a collection of small, loosely-coupled services==
- compared to a traditional monolithic architecture - all features and services are part of one large application
- Microservices are small - *each microservice implements only a small piece of an application's overall functionality*
- Microservices are **loosely coupled**: different microservices interact with each other using stable & well-defined APIs
    - this means they are independent of one another
![[Pasted image 20211212192303.png]]
- inventory service
- customer details service
- authentication service
- customer request service
- payment processing service
**each of these is its own codebase & a seperate running process (or processes). they can all be built, deployed, and scaled separately**

### Why use Microservices?
- **Modularity** - microservices encourage modularity
    - in monolithic apps, individual pieces becom tightly coupled, and complexity grows
    - eventually, it becomes very hard to change anything without breaking something
- **technological flexibility** - you don't need to use the same languages and technologies for every part of the app. you can use the best tool for each job
- **optimized flexibility** - you can scale individual parts of the app based upon resource usage and load
    - with a monolith, you have to scale up the entire application, even if only one aspect of the service needs to be scaled
- for smaller, simpler apps, a monolith might be easier to manage