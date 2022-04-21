[[Cloud]] [[AWS]] [[Azure]]

[The Twelve-Factor App (12factor.net)](https://12factor.net/)

Methodology for building SaaS / web apps that:
- use declarative formats for setup automation, to minimize time & cost for new devs joining the project
- have a clean contract with the underlying operating system, offering maximum portability between execution environments
- are suitable for deployment on modern cloud platforms, obviating the need for servers and sysadmin
- minimize divergence between development and production, enabling continuous deployment for maximum agility
- scale up without significant changes to tooling, architecture, or development practices

## 1. One codebase tracked in version control, many deploys
![[Pasted image 20220415155036.png]]
- if there are multiple codebases, it's not an app - it's a distributed system
  - each component in a distributed system is an app, and should individually comply with 12 factor
  - multiple apps sharing the same code is a violation of 12 factor - solution is to factor shared code into libraries which can be included through the dependency manages
- a *deploy* is a running instance of the app
  - typically production site, and one or more staging sites
  - additionally, every dev has a copy of the app running in their local dev environment, each of which is considered a deploy
- codebase is the same across all deploys, although different versions may be active in each deploy
  - e.g. dev has some commits on a branch not yet merged with staging, which also has some commits not yet deployed to prod

## 2. Explicitly declare and isolate dependencies
- never rely on implicit existence of system-wide packages
- declare **all** dependencies, completely & exactly, via "dependency declaration" manifest
- furthermore, it uses a **dependency isolation** tool during execution to ensure that no implicit dependencies 'leak in' from the surrounding system

## 3. Store config in the environment
- an app's *config* is everything that is likely to vary between deploys, including:
    - resource handles to the database, memcached, and other backing services
    - credentials to extenral services such as Amazon S3 or Twitter
    - per-deploy values such as the canonical hostname for the deploy
  - **do not store as constants in the code** - strict separation - config varies, code does not
    - can the codebase be made open-source at any moment, without compromising any credentials?
- store config in **environment variables**
  - easy to change
  - littile chance of accidentally checking them into the code repo accidentally (unlike config files)
  - language & OS agnostic (unlike config files)
- env vars are never grouped, instead independently managed for each deploy

## 4. treat backing services as attached resources
- *backing service* is any service that the app consumes over the network as part of its normal operation
  - e.g. datastores, messaging/queuing systems, SMTP services for outbound email, cache services
- **there should be no distinction between local & third party services**
  - a deploy of the 12-factor app should be able to swap out a local MySQL database with a managed one without any changes to the app's code
  - only the resource handle in the config needs to change
- each distinct backing service is a *resource* 
![[Pasted image 20220415172740.png]]
- resources can be attached & detached from deploys at will - facilitating use of backups

## 5. Strictly separate build & run stages
a codebase is transformed into a deploy through 3 stages:
1. **build stage** is a transform which converts code repo into an executable bundle known as a *build*
    - using a version of the code at a commit specified by the deployment process, the build stage fetches vendors dependencies and compiles binaries & assets
2. **release stage** takes the build & combines it with the deploy's current **config**.
    - resulting **release** contains both the build stage & the config and is ready for immediate execution in the execution environment
3. **run stage** (runtime) runs the app in the execution environment, by launching some set of the app's *processes* against a selected release
![[Pasted image 20220415173258.png]]
- keep the run stage with as few moving parts as possible, since problems that prevent an app from running can cause it to break in the middle of the night when no devs are on hand
- build stage can be more complex, since errors are always in the foreground for a dev who is driving the deploy

## 6. Execute the app as one or more *stateless* processes
- in the simplest case, the code is a stand-alone script, the execution env is a developer's local laptop with an

## 7. Export services via port binding
- 12-factor app is completely self-contained and does not rely on runtime injection of a webserver into the execution environment to create a web-facing servic
- web app exports HTTP as a service by binding to a port, and listening to requests coming in on that port
- in a local dev environment, the developer visits a service URL like `http://localhlost:5000/` to access the service exported by their app
- in deployment, a routing layer handles routing requests from a public-facing hostname to the port-bound web processes
  - this is typically implemented by using *dependency declaration* to add a webserver library to the app, such as tornado for python
  - this happens entirely in the `user space`, within the app's code
  - the contract with the execution evironment is binding to a port to serve requests
- HTTP is not the only service that can be exported by port binding - **any kind of server software can run via a process binding to a port, awaiting incoming requests**
- note that the port-binding approach means that one app can become the backing service for another app, by providing the URL to the backing app as a resource handle in the config for the consuming app

## 8. Scale out via the process model (concurrently)
![[Pasted image 20220418123304.png]]
- **processes are a first class citizen**, following [the unix process model for running service daemons](https://adam.herokuapp.com/past/2011/5/9/applying_the_unix_process_model_to_web_apps/)
  - developer can architect their app to handle diverse workloads by assigning each type of work to a *process type*
    - e.g. HTTP requests may be handled by a web process, and long-running background tasks handled by a worker process
- do not exclude individual processes from handling their own internal multiplexing, via threads inside the runtime VM, or the async/evented model found in tools such as node.js
- an individual VM can only grow so large, so the application must also be able to span multiple processes running on multiple physical machines
- this model truly shines when it comes time to scale out
  - **Share-nothing, horizontally partitionable nature of the 12-factor app processes means that adding more concurrency is a simple and reliable operation**
  - array of process types and number of processes of each type is known as the *process formation*
  - twelve-factor app processes should never daemonize or write PID files - instead, rely on the OS process manager, such as systemd, to:
      - manage output streams
      - respond to crashed processes,
      - handle user-initiated restarts and shutdowns

## 9. Maximize robustness with fast startup & graceful shutdown (Disposability)
- 12FA is **disposable**, meaning they can be started or stopped at a moment's notice
  - this facilitates fast elastic scaling, rapid deployment of code or config changes, and robustness of production deploys
  - processes should strive to minimize startup time
    - ideally a few second from launch command until the process is up & ready to receive requests/jobs
  - short startup time provides more agility for the release process and scaling up 
  - it also aids robustness because the process manager can more easily move processes to new physical machines when warranted

- **processes shut down gracefully when they recieve a SIGTERM signal from the process manager**
  - for a web process, graceful shutdown is achieved by ceasing to listen on the service port (thereby refusing any new requests), allowing any current requests to finish, and then exiting
    - implicit in this model is that **HTTP requests are short** (no more than a few seconds), or when long polling, the client should seamlessly attempt to reconnect when the connection is lost 

## 10. keep development, staging, and production as similar as possible

## 11. treat logs as event streams
- logs provide visibility into the behavior of a running app
- in server-based environments they are commonly written to a file on dist (a 'logfile')
- logs are ==stream of aggregated, time-ordered events collected from the output streams of all running processes and backing services==
- in their raw form, are typically text format with one event per line (though backtraces from exceptions may span multiple lines)
- no fixed beginning or end, but flow continuously as long as the app is operating
- **do not concern yoursely with routing or storage of output stream**
  - do not attempt to write to or manage logfiles
  - instead, each running process writes its event stream, unbuffered, to `stdout`
  - during local development, the dev will view this stream in the foreground of their terminal to observe the app's behavior
  - in staging / prod deploys, each process' stream will be captured by the execution env, collated together with all other streams from the app, and routed to one or more final destinations for viewing and long-term archival
    - these archival destinations are not visible nor configurable by the app, and instead are completely managed by the execution environment
    - use open-source log routers like Logplex or Fluentd
- event stream for an app can be routed to a file, or watched via realtime tail in a terminal
- stream can be sent to a log indexing and analysis system like splunk, or a data warehouse
  - enabling:
    - finding specific events in the past
    - large-scale graphing of trends
    - active alerting according to user-defined heuristics (such as an alert when the quality af errors per minute exceeds a certain threshold)

## 12. run admin/management tasks as one-off processes