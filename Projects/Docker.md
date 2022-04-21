[Projects/DevOps]] [[Cloud]]
# Docker architecture
## Client-server architecture
- daemon & client are separate binaries
  - client can be used to communicate with different daemons
- docker client issues commands to the docker daemon
- daemon handles:
  - building
  - running
  - distributing containers
- both communicate using a REST API
  - UNIX sockets
  - network interface
- daemon process is called `dockerd`
  - listens for Docker API requests & manages Docker objects:
    - images
    - containers
    - networks
    - volumes
- **Docker registries** stores docker images
  - similar to a github repo, but for Docker images
  - by default, set to DockerHub
  - can also run your own private registry
    - `docker image pull` to pull from registry
    - `docker image push` to push an image up to it
![[Pasted image 20220412150721.png]]
## Docker objects
### images
  - read-only template with instructions for creating a Docker container
  - based off a 'base image'
  - other commands can:
    - copy code over to your image
    - set working dir
    - run shell commands at startup
  - **create your own images**
    - can use Dockerfile
### containers
- runnable instance of the image it was created from
- you can:
  - create
  - stop
  - move
  - delete them
- connect a container to networks (more than 1)
- attach persistent storage
  - important data doesn't get lost when container is destroyed or recreated
- create a **new image** based on its current state
- isolated from other containers and the host machine
  - containers designed to run in parallel
  - can control each container's level of isolation 
### services (docker swarm)
- scale containers across multiple docker hosts
- multiple daemons working in parallel
  - communicate via same docker API
  - 2 types of nodes:
    1. managers (manage clusters)
    2. workers (execute tasks)
- define desired state
  - if one node goes down, docker swarm will run a new node to bring you back to desired state
- **load balances replicas** across all worker nodes in the cluster

## docker logging
`docker container logs sha12345` (note that services like swarm have a separate logging process)

## Docker engine
- modular in design
  - batteries included but replaceable
- based on an open-standards outline by the **Open Container Initiative**
  - image spec
  - container runtime spec
  - v 1.0 released in 2017
  - docker 1.11 (2016) used as much of the specification as possible
`runc`

### major components:
- docker client
- docker daemon
##### `containerd`
  - manage container lifecycle
    - start
    - stop
    - pause
    - delete
  - image management (push/pull)
##### `runc`
  - implementation of OCI container-runtime spec
  - lightweight CLI wrapper for libcontainer
  - creates containers
![[Pasted image 20220412162720.png]]
##### `shim`
- implementation of daemonless containers
- `conainerd` forks an instance of `runc` for each new container
- `runc` process exits after the container is created
- `shim` process becomes the container parent
  - this allows us to run 100s of containers without running 100s of `runc` instances
  - responsible for **keeping STDIN & STOUT streams open**
  - if daemon is restarted, the container doesn't terminate due to closed pipes
  - reports back exit status of a container to the docker 
  
### Create a container
  **`docker container run -it --name <NAME> <IMAGE>:<TAG>`**
  - use the CLI to execute a command
  - docker client uses the appropriate API payload
  - `POSTS` to the correct Docker API endpoint
  - the Docker daemon receives instructions, and then relays to `containerd` to start new container
  - daemon uses **gRPC** (a CRUD-style API)
    - create
    - read
    - update
    - delete
  - `containerd` creates an OCI bundle from the Docker image
    - tells `runc` to create a container using OCI bundle
    - `runc` interfaces with the OS kernel to get the constructs needed to create a container
      - this includes namespaces, `cgroups`, etc.
    - container is process is started as a child process
    - once the container starts, `runc` will exit
    - `shim` process takes over
![[Pasted image 20220412165630.png]]
# Docker images & containers
## Docker images
- template for your containers
- analogous to **classes** in programming
- like a stopped container
- comprised of multiple layers (each one is stacked)
- **build-time contstructs**, vs. containers as run-time constructs
- containers & images are dependent on one-another
  - can't delete an image until all containers are deleted
- **built from Dockerfiles**
  - contain instructions on how the container should be set up:
    - binaries
    - libraries
- containers are meant to be lightweight - keep them as small as possible
  - only install things that are necessary for the application

- Images are made of multiple layers
- each layer represents an instructtion in the image's `Dockerfile`
- each layer is read/write, *except* the last layer
- each layer is only a set of differences from the layer before it
- Containers add a new writeable layer on top of the underlying layers
- all changes made to a running container are made to the Container layer
![[Pasted image 20220413232044.png]]
## Containers
- runtime instance of an image
- top writable layer
- all changes are stored in the writable layer
- the writable layer is deleted when the container is deleted
- the image remains unchanged
![[Pasted image 20220413232324.png]]

# docker commands
## management commands
- `builder`: manage builds
- `config`: manage docker configs
### `container`: manage containers
  - `ls`: list containers
##### `run`: run a command in a new container
  - `--rm`: automatically remove the container when it exits
  - `-d`/`--detach`: run container in the background and print container ID
  - `-i`/`--interactive`: keep STDIN open even if not attached
  - `--name <string>`: assign a name to the container
  - `-p`/`--publish <list>`: publish a container's port(s) by mapping it to the host
    - `-P`/`--publish-all`: automatically publish all exposed container ports to random ports in host machine
  - `-t`/`--tty`: allocate a psuedo-TTY
  - `-v`/`volume <list>`: bind a mount volume
  - `--rm`: automatically remove the container when it exits
  - `--mount <mount>`: attach a filesystem mount to the container
  - `--network <string>`: connect a container to a network (defaults ta `default`)

  - `inspect`: display detailed information on one or more containers
  - `top`: display the running processes of a container
  - `attach`: attach local standard input, output, and error streams to a running container
  - `prune`: clear out containers that are not running
  - `port [<name>]`: list all port mappings 
- `engine`: manage the docker engine

`docker container run -P -d nginx`: run nginx container on a random port, detached from current shell session
`docker container inspect uc2452`: show info of new container, find IP address, then `curl 127.0.0.2`
`docker attach uc2452` to attach to STDIN & STOUT - configured to send to access logs, so we won't have access to shell
   - when you detach an attached container, the container will **stop**

#### `image`: manage images
  - `ls`: list images (`--all`/`-a` for all) 
  - `pull`: pull an image or a repository from a registry
  - `push`: push an image or a repository to a registry
  - `inspect`: return low-level information on Docker objects
  - `import`: import the contents from a tarball to create a filesystem image
- `network`: manage network
- `node`: Manage swarm nodes
- `plugin`: 
- `secret`:  
- `service`: 
- `stack`: 
- `swarm`: 
- `trust`: 
- `volume`: 

# Ports
## Expose
- expose a single port or a range of ports
- this does not publish the port
- use `--expose <port>`
## Publish
- map a container's port to a host's port
- `-p`/`publish <list>`: publish a container's port(s) by mapping it to the host (host_machine:container `30:3000`
  - `-P`/`--publish-all`: used to publish all exposed ports to random ports
- `docker container run -d --expose 3000 -p 8081:80/tcp -p 8081:80/udp --name example_contaner busybox`
- `curl localhost:8081`
  - need to have a process listening on container to fetch data!

# Execute shell commands in container
- Dockerfile
- `docker run -it busybox /bin/bash`
- `docker container exec -it busybox ls /usr/share/my_app/`
  - will only be run while the container's primary process is running
  -  command will be run in the default directory of the container, unless working directory directive is set, in which case the command will execute in that dir

Commands can be:
1. "one and done" task (quick executions)
2. long-running commands run for the lifecycle of the container
3
# Networking
5 types of networks:
1. default
    - installed by default by docker
2. bridge
    - isolate applications from eachother
3. host
    - does not isolate the container from the host
    - none (no connectivity)

### docker network
```
[cloud_user@ip-10-0-1-97 ~]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
5c2e6d8328c1   bridge    bridge    local
f6724ac407e7   host      host      local
aff40a02a25f   none      null      local
[cloud_user@ip-10-0-1-97 ~]$ docker run -d web1 httpd:2.4
Unable to find image 'web1:latest' locally
docker: Error response from daemon: pull access denied for web1, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.
See 'docker run --help'.
[cloud_user@ip-10-0-1-97 ~]$ clear
[cloud_user@ip-10-0-1-97 ~]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
5c2e6d8328c1   bridge    bridge    local
f6724ac407e7   host      host      local
aff40a02a25f   none      null      local
[cloud_user@ip-10-0-1-97 ~]$ docker run -d --name web1 httpd:2.4
77c144e8785402612593890264ccf623bcbbbb2fe114817f67fed1c055bbf5b5
[cloud_user@ip-10-0-1-97 ~]$ docker inspect web1
[
    {
        "Id": "77c144e8785402612593890264ccf623bcbbbb2fe114817f67fed1c055bbf5b5",
        "Created": "2021-12-29T14:12:38.588540071Z",
        "Path": "httpd-foreground",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 13588,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-12-29T14:12:39.02557942Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:dabbfbe0c57b6e5cd4bc089818d3f664acfad496dc741c9a501e72d15e803b34",
        "ResolvConfPath": "/var/lib/docker/containers/77c144e8785402612593890264ccf623bcbbbb2fe114817f67fed1c055bbf5b5/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/77c144e8785402612593890264ccf623bcbbbb2fe114817f67fed1c055bbf5b5/hostname",
        "HostsPath": "/var/lib/docker/containers/77c144e8785402612593890264ccf623bcbbbb2fe114817f67fed1c055bbf5b5/hosts",
        "LogPath": "/var/lib/docker/containers/77c144e8785402612593890264ccf623bcbbbb2fe114817f67fed1c055bbf5b5/77c144e8785402612593890264ccf623bcbbbb2fe114817f67fed1c055bbf5b5-json.log",
        "Name": "/web1",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/06c00e09d03e2e9967bdb595225657d02b73d6ce8de1ae715a400053bc8dbbfc-init/diff:/var/lib/docker/overlay2/d0138301b8c954cfe6767732f79b066f3f6539bec30688a8fc16208ea65261cd/diff:/var/lib/docker/overlay2/c237ea27833577dd8a930a9bc15d06b84993464669d6d50f0d8989f3f023088c/diff:/var/lib/docker/overlay2/29a1b68d797d55cb4d0ec448949799dd5ccc8a80ccf1113a19ff3e181d02b36e/diff:/var/lib/docker/overlay2/f12a749eae8c109f53200e24526a163b96a8818f3828493a753c1f21f7de112d/diff:/var/lib/docker/overlay2/3489fb08b23b98110a22fdcee39b949fa08a66dd31d432223fbcb589cfbd2549/diff",
                "MergedDir": "/var/lib/docker/overlay2/06c00e09d03e2e9967bdb595225657d02b73d6ce8de1ae715a400053bc8dbbfc/merged",
                "UpperDir": "/var/lib/docker/overlay2/06c00e09d03e2e9967bdb595225657d02b73d6ce8de1ae715a400053bc8dbbfc/diff",
                "WorkDir": "/var/lib/docker/overlay2/06c00e09d03e2e9967bdb595225657d02b73d6ce8de1ae715a400053bc8dbbfc/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "77c144e87854",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/apache2/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "HTTPD_PREFIX=/usr/local/apache2",
                "HTTPD_VERSION=2.4.52",
                "HTTPD_SHA256=0127f7dc497e9983e9c51474bed75e45607f2f870a7675a86dc90af6d572f5c9",
                "HTTPD_PATCHES="
            ],
            "Cmd": [
                "httpd-foreground"
            ],
            "Image": "httpd:2.4",
            "Volumes": null,
            "WorkingDir": "/usr/local/apache2",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGWINCH"
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "c7ad23ad4d78bec18ce4527a868e6a81d9aa7c442e0f539af02ec215e93ed0ce",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "80/tcp": null
            },
            "SandboxKey": "/var/run/docker/netns/c7ad23ad4d78",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "ef07bf27a8c114c93a7bb494d9b4b2694e350b773564bfadb23764264b4c8185",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "5c2e6d8328c1fce957986838978ab4696448abaa2cd1e9e19c46e8d1538adfce",
                    "EndpointID": "ef07bf27a8c114c93a7bb494d9b4b2694e350b773564bfadb23764264b4c8185",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
[cloud_user@ip-10-0-1-97 ~]$ docker run --rm -it busybox
/ # ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
6: eth0@if7: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.3/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
/ # ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.132 ms
64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.100 ms
64 bytes from 172.17.0.2: seq=2 ttl=64 time=0.091 ms
64 bytes from 172.17.0.2: seq=3 ttl=64 time=0.094 ms
^C
--- 172.17.0.2 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 0.091/0.104/0.132 ms
/ # wget 172.17.0.2
Connecting to 172.17.0.2 (172.17.0.2:80)
saving to 'index.html'
index.html           100% |*********************************************************************|    45  0:00:00 ETA
'index.html' saved
/ # docker ps -a
sh: docker: not found
/ # exit
[cloud_user@ip-10-0-1-97 ~]$ docker ps -a
CONTAINER ID   IMAGE       COMMAND              CREATED         STATUS         PORTS     NAMES
77c144e87854   httpd:2.4   "httpd-foreground"   4 minutes ago   Up 4 minutes   80/tcp    web1
[cloud_user@ip-10-0-1-97 ~]$ docker network create test_application
cfa3f50675db0adfb743060cf31dd6e47613a7de3ccbb23eed56fe1828fce64b
[cloud_user@ip-10-0-1-97 ~]$ docker network ls
NETWORK ID     NAME               DRIVER    SCOPE
5c2e6d8328c1   bridge             bridge    local
f6724ac407e7   host               host      local
aff40a02a25f   none               null      local
cfa3f50675db   test_application   bridge    local
[cloud_user@ip-10-0-1-97 ~]$ docker run -d --name web2 --network test_application httpd:2.4
9a78152ccfbdf9876412ef708ef0e9de2bac8e122b95b9d8e5c9a653b5055e78
[cloud_user@ip-10-0-1-97 ~]$ docker network ls
NETWORK ID     NAME               DRIVER    SCOPE
5c2e6d8328c1   bridge             bridge    local
f6724ac407e7   host               host      local
aff40a02a25f   none               null      local
cfa3f50675db   test_application   bridge    local
[cloud_user@ip-10-0-1-97 ~]$ docker ps -a
CONTAINER ID   IMAGE       COMMAND              CREATED         STATUS         PORTS     NAMES
9a78152ccfbd   httpd:2.4   "httpd-foreground"   2 minutes ago   Up 2 minutes   80/tcp    web2
77c144e87854   httpd:2.4   "httpd-foreground"   7 minutes ago   Up 7 minutes   80/tcp    web1
[cloud_user@ip-10-0-1-97 ~]$ docker inspect web2
[
    {
        "Id": "9a78152ccfbdf9876412ef708ef0e9de2bac8e122b95b9d8e5c9a653b5055e78",
        "Created": "2021-12-29T14:18:04.037967299Z",
        "Path": "httpd-foreground",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 13917,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-12-29T14:18:04.463553226Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:dabbfbe0c57b6e5cd4bc089818d3f664acfad496dc741c9a501e72d15e803b34",
        "ResolvConfPath": "/var/lib/docker/containers/9a78152ccfbdf9876412ef708ef0e9de2bac8e122b95b9d8e5c9a653b5055e78/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/9a78152ccfbdf9876412ef708ef0e9de2bac8e122b95b9d8e5c9a653b5055e78/hostname",
        "HostsPath": "/var/lib/docker/containers/9a78152ccfbdf9876412ef708ef0e9de2bac8e122b95b9d8e5c9a653b5055e78/hosts",
        "LogPath": "/var/lib/docker/containers/9a78152ccfbdf9876412ef708ef0e9de2bac8e122b95b9d8e5c9a653b5055e78/9a78152ccfbdf9876412ef708ef0e9de2bac8e122b95b9d8e5c9a653b5055e78-json.log",
        "Name": "/web2",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "test_application",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/d478fa356ed6d331c7a42e9de72f3e8c2ef0592fe8f415f670efab0d1df0e95c-init/diff:/var/lib/docker/overlay2/d0138301b8c954cfe6767732f79b066f3f6539bec30688a8fc16208ea65261cd/diff:/var/lib/docker/overlay2/c237ea27833577dd8a930a9bc15d06b84993464669d6d50f0d8989f3f023088c/diff:/var/lib/docker/overlay2/29a1b68d797d55cb4d0ec448949799dd5ccc8a80ccf1113a19ff3e181d02b36e/diff:/var/lib/docker/overlay2/f12a749eae8c109f53200e24526a163b96a8818f3828493a753c1f21f7de112d/diff:/var/lib/docker/overlay2/3489fb08b23b98110a22fdcee39b949fa08a66dd31d432223fbcb589cfbd2549/diff",
                "MergedDir": "/var/lib/docker/overlay2/d478fa356ed6d331c7a42e9de72f3e8c2ef0592fe8f415f670efab0d1df0e95c/merged",
                "UpperDir": "/var/lib/docker/overlay2/d478fa356ed6d331c7a42e9de72f3e8c2ef0592fe8f415f670efab0d1df0e95c/diff",
                "WorkDir": "/var/lib/docker/overlay2/d478fa356ed6d331c7a42e9de72f3e8c2ef0592fe8f415f670efab0d1df0e95c/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "9a78152ccfbd",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/apache2/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "HTTPD_PREFIX=/usr/local/apache2",
                "HTTPD_VERSION=2.4.52",
                "HTTPD_SHA256=0127f7dc497e9983e9c51474bed75e45607f2f870a7675a86dc90af6d572f5c9",
                "HTTPD_PATCHES="
            ],
            "Cmd": [
                "httpd-foreground"
            ],
            "Image": "httpd:2.4",
            "Volumes": null,
            "WorkingDir": "/usr/local/apache2",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGWINCH"
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "161ec21012cec049cf25a1703e9282375f22a99f312101894f3bcb2a17784194",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "80/tcp": null
            },
            "SandboxKey": "/var/run/docker/netns/161ec21012ce",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "test_application": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "9a78152ccfbd"
                    ],
                    "NetworkID": "cfa3f50675db0adfb743060cf31dd6e47613a7de3ccbb23eed56fe1828fce64b",
                    "EndpointID": "91c90813843acdf94bf02a3da99d7fb6ff91730d9962637980e1e5c18980b531",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
[cloud_user@ip-10-0-1-97 ~]$ docker run --rm -it --network test_application busybox
/ # ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
11: eth0@if12: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether 02:42:ac:12:00:03 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.3/16 brd 172.18.255.255 scope global eth0
       valid_lft forever preferred_lft forever
/ # 172.18.0.2
sh: 172.18.0.2: not found
/ # ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.137 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.098 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.098 ms
64 bytes from 172.18.0.2: seq=3 ttl=64 time=0.092 ms
^R
64 bytes from 172.18.0.2: seq=4 ttl=64 time=0.093 ms
64 bytes from 172.18.0.2: seq=5 ttl=64 time=0.109 ms
^C
--- 172.18.0.2 ping statistics ---
6 packets transmitted, 6 packets received, 0% packet loss
round-trip min/avg/max = 0.092/0.104/0.137 ms
/ # ping web2
PING web2 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.071 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.092 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.104 ms
^C
--- web2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.071/0.089/0.104 ms
/ # wget web2
Connecting to web2 (172.18.0.2:80)
saving to 'index.html'
index.html           100% |*********************************************************************|    45  0:00:00 ETA
'index.html' saved
/ # ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
^C
--- 172.17.0.2 ping statistics ---
21 packets transmitted, 0 packets received, 100% packet loss
/ # exit
[cloud_user@ip-10-0-1-97 ~]$ docker run -d --name web3 --network host httpd:2.4
a6a4b70fd7fe6ce45741b721f665c85a60f11ab280dad325021b0d8cb6efd0a7
[cloud_user@ip-10-0-1-97 ~]$ docker ps -a
CONTAINER ID   IMAGE       COMMAND              CREATED          STATUS          PORTS     NAMES
a6a4b70fd7fe   httpd:2.4   "httpd-foreground"   7 seconds ago    Up 6 seconds              web3
9a78152ccfbd   httpd:2.4   "httpd-foreground"   9 minutes ago    Up 9 minutes    80/tcp    web2
77c144e87854   httpd:2.4   "httpd-foreground"   15 minutes ago   Up 15 minutes   80/tcp    web1
[cloud_user@ip-10-0-1-97 ~]$ wget localhost
--2021-12-29 09:29:31--  http://localhost/
Resolving localhost (localhost)... ::1, 127.0.0.1
Connecting to localhost (localhost)|::1|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 45 [text/html]
Saving to: ‘index.html’

100%[===========================================================================>] 45          --.-K/s   in 0s

2021-12-29 09:29:31 (7.75 MB/s) - ‘index.html’ saved [45/45]

[cloud_user@ip-10-0-1-97 ~]$ docker stop web35
Error response from daemon: No such container: web35
[cloud_user@ip-10-0-1-97 ~]$ docker stop web3
web3
[cloud_user@ip-10-0-1-97 ~]$ wget localhost
--2021-12-29 09:31:11--  http://localhost/
Resolving localhost (localhost)... ::1, 127.0.0.1
Connecting to localhost (localhost)|::1|:80... failed: Connection refused.
Connecting to localhost (localhost)|127.0.0.1|:80... failed: Connection refused.
[cloud_user@ip-10-0-1-97 ~]$ docker start web3
web3
[cloud_user@ip-10-0-1-97 ~]$ wget localhost
--2021-12-29 09:31:25--  http://localhost/
Resolving localhost (localhost)... ::1, 127.0.0.1
Connecting to localhost (localhost)|::1|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 45 [text/html]
Saving to: ‘index.html.1’

100%[===========================================================================>] 45          --.-K/s   in 0s

2021-12-29 09:31:25 (7.18 MB/s) - ‘index.html.1’ saved [45/45]

[cloud_user@ip-10-0-1-97 ~]$ docker run --rm -it --network host busybox
/ # ping web3
ping: bad address 'web3'
/ # wget localhost
Connecting to localhost (127.0.0.1:80)
saving to 'index.html'
index.html           100% |*********************************************************************|    45  0:00:00 ETA
'index.html' saved
/ # ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.103 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.084 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.090 ms
^C
--- 172.18.0.2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.084/0.092/0.103 ms
```

Flask application
1. create the build files
2. build & set up environment
3. run, evaluate, upgrade
4. upgrade to gunicorn
5. build a production image
```
[cloud_user@ip-10-0-1-188 ~]$ ll
total 4
-rw-r--r--. 1 cloud_user cloud_user   1 Aug 13 12:11 init_pass
drwxr-xr-x. 4 cloud_user cloud_user 155 Dec 29 15:37 notes
[cloud_user@ip-10-0-1-188 ~]$ cd notes
[cloud_user@ip-10-0-1-188 notes]$ ls -la
total 40
drwxr-xr-x. 4 cloud_user cloud_user   155 Dec 29 15:37 .
drwx------. 5 cloud_user cloud_user   153 Dec 29 15:37 ..
-rw-r--r--. 1 cloud_user cloud_user   422 Dec 29 15:37 config.py
-rw-r--r--. 1 cloud_user cloud_user   185 Dec 29 15:37 .env
-rw-r--r--. 1 cloud_user cloud_user  1347 Dec 29 15:37 .gitignore
-rw-r--r--. 1 cloud_user cloud_user  5087 Dec 29 15:37 __init__.py
-rw-r--r--. 1 cloud_user cloud_user   971 Dec 29 15:37 models.py
-rw-r--r--. 1 cloud_user cloud_user   226 Dec 29 15:37 Pipfile
-rw-r--r--. 1 cloud_user cloud_user 10219 Dec 29 15:37 Pipfile.lock
drwxr-xr-x. 2 cloud_user cloud_user   124 Dec 29 15:37 static
drwxr-xr-x. 2 cloud_user cloud_user   165 Dec 29 15:37 templates
[cloud_user@ip-10-0-1-188 notes]$ cat config.py
import os

db_host = os.environ.get('DB_HOST', default='localhost')
db_name = os.environ.get('DB_NAME', default='notes')
db_user = os.environ.get('DB_USERNAME', default='notes')
db_password = os.environ.get('DB_PASSWORD', default='')
db_port = os.environ.get('DB_PORT', default='5432')

SQLALCHEMY_TRACK_MODIFICATIONS = False
SQLALCHEMY_DATABASE_URI = f"postgresql://{db_user}:{db_password}@{db_host}:{db_port}/{db_name}"
[cloud_user@ip-10-0-1-188 notes]$ vim .dockerignore
[cloud_user@ip-10-0-1-188 notes]$ cat .dockerignore
.dockerignore
Dockerfile
.gitignore
Pipfile.lock
migrations/
[cloud_user@ip-10-0-1-188 notes]$ vim Dockerfile
[cloud_user@ip-10-0-1-188 notes]$ cat Dockerfile
FROM python:3
# dependencies that our app requires (pipenv)
ENV PYBASE /pybase
ENV PYTHONUSERBASE $PYBASE
ENV PATH $PYBASE/bin:$PATH
RUN pip install pipenv

# normally pipenv compares Pipfile.lock to pipfile (dont want this)
WORKDIR /tmp
COPY Pipfile .
RUN pipenv lock
RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile

# copy application code into the image & start the image
COPY . /app/notes
WORKDIR /app/notes
EXPOSE 80
CMD ["flask", "run", "--port=80", "--host=0.0.0.0"]
[cloud_user@ip-10-0-1-188 notes]$ docker build -t notesapp0.1 .
Sending build context to Docker daemon  261.6kB
Step 1/13 : FROM python:3
 ---> a5d7930b60cc
Step 2/13 : ENV PYBASE /pybase
 ---> Running in 9a0448e90958
Removing intermediate container 9a0448e90958
 ---> c9a184a7a032
Step 3/13 : ENV PYTHONUSERBASE $PYBASE
 ---> Running in a943db5e9769
Removing intermediate container a943db5e9769
 ---> e9711b65b313
Step 4/13 : ENV PATH $PYBASE/bin:$PATH
 ---> Running in cdcf649ed665
Removing intermediate container cdcf649ed665
 ---> 458d81229bcb
Step 5/13 : RUN pip install pipenv
 ---> Running in 045986e8b48a
Collecting pipenv
  Downloading pipenv-2021.11.23-py2.py3-none-any.whl (3.6 MB)
Requirement already satisfied: setuptools>=36.2.1 in /usr/local/lib/python3.10/site-packages (from pipenv) (57.5.0)
Collecting virtualenv
  Downloading virtualenv-20.11.2-py2.py3-none-any.whl (6.5 MB)
Requirement already satisfied: pip>=18.0 in /usr/local/lib/python3.10/site-packages (from pipenv) (21.2.4)
Collecting virtualenv-clone>=0.2.5
  Downloading virtualenv_clone-0.5.7-py3-none-any.whl (6.6 kB)
Collecting certifi
  Downloading certifi-2021.10.8-py2.py3-none-any.whl (149 kB)
Collecting platformdirs<3,>=2
  Downloading platformdirs-2.4.1-py3-none-any.whl (14 kB)
Collecting six<2,>=1.9.0
  Downloading six-1.16.0-py2.py3-none-any.whl (11 kB)
Collecting filelock<4,>=3.2
  Downloading filelock-3.4.2-py3-none-any.whl (9.9 kB)
Collecting distlib<1,>=0.3.1
  Downloading distlib-0.3.4-py2.py3-none-any.whl (461 kB)
Installing collected packages: six, platformdirs, filelock, distlib, virtualenv-clone, virtualenv, certifi, pipenv
Successfully installed certifi-2021.10.8 distlib-0.3.4 filelock-3.4.2 pipenv-2021.11.23 platformdirs-2.4.1 six-1.16.0 virtualenv-20.11.2 virtualenv-clone-0.5.7
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
WARNING: You are using pip version 21.2.4; however, version 21.3.1 is available.
You should consider upgrading via the '/usr/local/bin/python -m pip install --upgrade pip' command.
Removing intermediate container 045986e8b48a
 ---> 622a67e9b23d
Step 6/13 : WORKDIR /tmp
 ---> Running in aa9573f36edc
Removing intermediate container aa9573f36edc
 ---> 4e1c1e2f23ac
Step 7/13 : COPY Pipfile .
 ---> ea3840344ebe
Step 8/13 : RUN pipenv lock
 ---> Running in ad205490acc3
Creating a virtualenv for this project...
Pipfile: /tmp/Pipfile
Using /usr/local/bin/python (3.10.1) to create virtualenv...
⠹ Creating virtual environment...created virtual environment CPython3.10.1.final.0-64 in 851ms
  creator CPython3Posix(dest=/root/.local/share/virtualenvs/tmp-XVr6zr33, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/root/.local/share/virtualenv)
    added seed packages: pip==21.3.1, setuptools==60.1.1, wheel==0.37.1
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

✔ Successfully created virtual environment!
Virtualenv location: /root/.local/share/virtualenvs/tmp-XVr6zr33
Locking [dev-packages] dependencies...
Building requirements...
Resolving dependencies...
⠙ Locking..✔ Success!
Locking [packages] dependencies...
Building requirements...
Resolving dependencies...
⠏ Locking..✔ Success!
Updated Pipfile.lock (730408)!
Removing intermediate container ad205490acc3
 ---> 45c34321c348
Step 9/13 : RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile
 ---> Running in 93c45b424dc9
Installing dependencies from Pipfile.lock (730408)...
Removing intermediate container 93c45b424dc9
 ---> 514723d94409
Step 10/13 : COPY . /app/notes
 ---> 2e28fd81c744
Step 11/13 : WORKDIR /app/notes
 ---> Running in c439164ba3bd
Removing intermediate container c439164ba3bd
 ---> 47fdf1f2d316
Step 12/13 : EXPOSE 80
 ---> Running in 7377abeea7d5
Removing intermediate container 7377abeea7d5
 ---> f161dc532035
Step 13/13 : CMD ["flask", "run", "--port=80", "--host=0.0.0.0"]
 ---> Running in d0e5ecd46890
Removing intermediate container d0e5ecd46890
 ---> a0642a311c95
Successfully built a0642a311c95
Successfully tagged notesapp0.1:latest
[cloud_user@ip-10-0-1-188 notes]$ docker images
REPOSITORY    TAG           IMAGE ID       CREATED          SIZE
notesapp0.1   latest        a0642a311c95   39 seconds ago   1.02GB
python        3             a5d7930b60cc   8 days ago       917MB
postgres      12.1-alpine   76780864f8de   23 months ago    154MB
[cloud_user@ip-10-0-1-188 notes]$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED             STATUS             PORTS                                       NAMES
19cfe99ac26d   postgres:12.1-alpine   "docker-entrypoint.s…"   About an hour ago   Up About an hour   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   notesdb

[cloud_user@ip-10-0-1-188 notes]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
2b8b492bb562   bridge    bridge    local
6363de4bb0ca   host      host      local
5417ae99a478   none      null      local
0d536510a7bb   notes     bridge    local
[cloud_user@ip-10-0-1-188 notes]$ docker run --rm -it --network notes -v /home/cloud_user/notes/migrations:/app/notes/migrations notesapp0.1 bash
root@c2ca97aa14a9:/app/notes# flask db init
  Creating directory /app/notes/migrations/versions ...  done
  Generating /app/notes/migrations/README ...  done
  Generating /app/notes/migrations/alembic.ini ...  done
  Generating /app/notes/migrations/env.py ...  done
  Generating /app/notes/migrations/script.py.mako ...  done
  Please edit configuration/connection/logging settings in '/app/notes/migrations/alembic.ini' before proceeding.
root@c2ca97aa14a9:/app/notes# ls -l migrations/
total 16
-rw-r--r--. 1 root root   41 Dec 29 21:57 README
-rw-r--r--. 1 root root  857 Dec 29 21:57 alembic.ini
-rw-r--r--. 1 root root 2767 Dec 29 21:57 env.py
-rw-r--r--. 1 root root  494 Dec 29 21:57 script.py.mako
drwxr-xr-x. 2 root root    6 Dec 29 21:57 versions
root@c2ca97aa14a9:/app/notes# flask db migrate
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.autogenerate.compare] Detected added table 'user'
INFO  [alembic.autogenerate.compare] Detected added table 'note'
f  Generating /app/notes/migrations/versions/0a52c1890731_.py ...  done
root@c2ca97aa14a9:/app/notes# flask db upgrade
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 0a52c1890731, empty message
root@c2ca97aa14a9:/app/notes# docker run --rm -it notes -p 80:80 notesapp0.1
bash: docker: command not found
root@c2ca97aa14a9:/app/notes# exit
exit
[cloud_user@ip-10-0-1-188 notes]$ docker run --rm -it notes -p 80:80 notesapp0.1
Unable to find image 'notes:latest' locally
docker: Error response from daemon: pull access denied for notes, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.
See 'docker run --help'.
[cloud_user@ip-10-0-1-188 notes]$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: ^C
[cloud_user@ip-10-0-1-188 notes]$ docker run --rm -it --network notes -p 80:80 notesapp0.1
 * Serving Flask app '.' (lazy loading)
 * Environment: development
 * Debug mode: on
 * Running on all addresses.
   WARNING: This is a development server. Do not use it in a production deployment.
 * Running on http://172.18.0.3:80/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 950-515-708
24.160.67.180 - - [29/Dec/2021 22:02:23] "GET / HTTP/1.1" 302 -
24.160.67.180 - - [29/Dec/2021 22:02:24] "GET /notes HTTP/1.1" 302 -
24.160.67.180 - - [29/Dec/2021 22:02:24] "GET /log_in HTTP/1.1" 200 -
24.160.67.180 - - [29/Dec/2021 22:02:24] "GET /static/bulma.min.css HTTP/1.1" 200 -
24.160.67.180 - - [29/Dec/2021 22:02:24] "GET /static/highlight.min.css HTTP/1.1" 200 -
24.160.67.180 - - [29/Dec/2021 22:02:24] "GET /static/highlight.min.js HTTP/1.1" 200 -
24.160.67.180 - - [29/Dec/2021 22:02:24] "GET /static/app.js HTTP/1.1" 200 -
24.160.67.180 - - [29/Dec/2021 22:02:24] "GET /static/styles.css HTTP/1.1" 200 -
24.160.67.180 - - [29/Dec/2021 22:02:25] "GET /favicon.ico HTTP/1.1" 404 -
24.160.67.180 - - [29/Dec/2021 22:02:44] "GET /sign_up HTTP/1.1" 200 -
24.160.67.180 - - [29/Dec/2021 22:02:44] "GET /static/bulma.min.css HTTP/1.1" 304 -
24.160.67.180 - - [29/Dec/2021 22:02:44] "GET /static/highlight.min.css HTTP/1.1" 304 -
24.160.67.180 - - [29/Dec/2021 22:02:44] "GET /static/highlight.min.js HTTP/1.1" 304 -
24.160.67.180 - - [29/Dec/2021 22:02:44] "GET /static/styles.css HTTP/1.1" 304 -
24.160.67.180 - - [29/Dec/2021 22:02:44] "GET /static/app.js HTTP/1.1" 304 -
24.160.67.180 - - [29/Dec/2021 22:02:53] "POST /sign_up HTTP/1.1" 302 -
24.160.67.180 - - [29/Dec/2021 22:02:53] "GET /log_in HTTP/1.1" 200 -
```