[[Nix]] [[DevOps]] [[Docker]]

**==Easily run other NixOS instances as containers ==**
- NixOS containers share the Nix store of the host, making container creation very efficient
- 2 ways to create:
1. imperatively with `nixos-container` command
2. declaratively with `configration.nix`
	- your containers will get upgraded along with your host system when you run `nixos-rebuild`

### Imperative Container management
- only possible as root
- `nixos-container create foo`
	- creates the container's root directory in `/var/lib/nixos-containers/foo`, & a small config file in `etc/nixos-containers/foo.conf`
	- it also builds the container's initial system configuration and stores it in `/nix/var/nix/profiles/per-container/foo/system`
- you can modify the initial configuration of the container on the command line
	- for instance,
```nix
# nixos-container create foo --config '
services.openssh.enable = true;
users.users.root.openssh.authorizedKeys.keys = ["ssh-dss AAAAB3N..."];'
```

- by default, the next free address in the `10.233.0.0/16` subnet will be chosen ;1as a container IP
- this behavior can be altered by setting `--host-address` and `--local-address`
```nix
# nixos-container create test --config-file test-container.nix \
--local-address 10.235.1.2 --host-address 10.235.1.1
```
- *creating a container **does not start it***
	- to start, run `nixos-container start foo`
- this command will return as soon as the container has booted and has reached `multi-user.target`
- on the host machine, the container runs within a systemd unit called `container@container-name.service`
	- if something goes wrong, you can get status info using `systemctl`
- if the container starts successfully, log in as root with `nixos-container root-login foo`
	- only the host can do this (since there is no authencitation)
- you can also get a regular login prompt using the `login` operation, which is available to all users on the host
- execute arbitrary commands with `nixos-container run foo -- uname -a`
- to change container config, update `/var/lib/container/name/etc/nixos/configuration.nix`, then run `nixos-container update foo`
	- alternatively, change the config from within the container & run `nixos-rebuild switch` inside the container
		- container does not have a copy of the NixOS channel, so you should run `nix-channel --update` first
- containers can be started & stopped by:
	- `nixos-container stop` / `nixos-container start`
	- or by using `systemctl` on the container's service unit
- to destroy a container, including its filesystem, use `nixos-container destroy foo`

## Declarative Container Specification
- specify in the host's `configuration.nix`, e.g.:
```nix
containers.database = 
	{ config =
		{ config, pkgs, ... }:
		{ services.postgresql.enable = true;
		services.postresql.packages = pkgs.postgresql_14;
		};
	};
```

- if you run `nixos-rebuild switch`, the container will be built or updated-in-place
- use `containers.database.autoStart = true` to start it automatically on boot

- by default, declarative containers share the networj namespace of the host
	- that means they can listen on privileged ports
	- however, they cannot change the network configuration

- to disable the container, just removve it from `configuration.nix`
	- this **will not delete the root directory of the container**
	- container filesystems can be destroyed imperatively bi `nixos-container destroy foo`
- start & stop declarative containers with the corresponding systemd service

# Container networking
```nix
containers.database = {
	privateNetwork = true;
	hostAddress = "192.168.100.10";
	localAddress = "192.168.100.11";
};
```
- this gives the container a private ethernet interface with IP address `192.168.100.11`, which is hooked up to a virtual Ethernet interface on the host with IP address `192.168.100.10`
- imperative containers get their own private IPv4 address in the range `10.233.0.0/16`
	- get the container's IPv4 address with: `nixos-container show-ip foo` -> `10.233.4.2`
	- `ping -c1 10.233.4.2` -> `64 bytes from ...`

- networking is implemented using a pair of virtual Ethernet devices
	 - the networking interface in the container is called `eth0`, while the matching interface in the host is called `ve-container-name`
	 - the container has its own network namespace, & the `CAP_NET_ADMIN` capability
	 - so, it can perform arbitrary network configuration such as setting up firewall rules, without affecting or having access to the host's network
- **by default, containers cannot talk to the outside network**
	- if you want that capability, **set up Network Address Translation (NAT) rules on the host to rewrite container traffic to use your external IP addresses**:
```nix
networking.nat.enable = true;
networking.nat.internalInterfaces = ["ve-+"];  # wildcard that matches all container interfaces
networking.nat.externalInterfaces = "eth0";
```
where `eth0` should be replaced with the desired external interface

- if you are using network manager, you need to explicitly prevent it from managing container interfaces:
`networking.networkmanager.unmanaged = ["interface-name:ve-*"];`