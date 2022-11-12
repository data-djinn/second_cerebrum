[[Nix]] [[Homelab]]

## Why Nix?
- most package managers (dpkg, rpm, brew) mutate the global state of the system
	- if `hello-1.0` installs a program to `/usr/bin/hello`, you cannot install `foo-1.1` as well, unless you change the installation paths or the binary name
	- changing the name means breaking users of that binary
- if you need 2 versions of mysqql (e.g. 5.2 & 5.5), you need to create a new package that changes all the paths of mysql dependencies (example `-5.5` && `-5,2` suffixes) & make sure the libraries don't collide
- you can use containers to solve this issue, but now you need orchestration tools, monitoring machines, volumes to share data, an.d additional overhead
- python devs could use `venv`, but how do you mix with other stacks? how do you avoid recompiling the same thing when it could instead be shared? how do you set up your dev tools to point to the different directories where libraries are installed?
==Nix solves all this at the packaging level - one tool to rule them all==

### Purely functional
- Nix makes *no assumptions about the global state of the system*
- the core of the Nix system is `/nix/store`, containing *derivations* (not packages)
	- derivations are stored like `/nix/store/hash-pkgname
	- the hash uniquely identifies the derivation
	- the pkgname is the name of the derivation
	- e.g. `/nix/store/s4zia7hhqkin1di0f187b79sa2srhv6k-bash-4.2-p45/`
		- this means there's no `/bin/bash`
		- only the self-contained build output in `/nix/store/`
		- Nix arranges for binaries to appear in your `PATH` as appropriate
	- all packages stored in `/nix/store/` are **immutable**
##### So what?
- so you can run any combination of any packages, all on the same system with no conflicts
	- no dependency hell, no need for a dependency resolution algorithm

#### Mutable vs immutable
- when upgrading a lib, most package managers replace it in-place
	- all new apps run afterwards with the new lib, without being recompiled
	- ultimately, all packages refer dynamically to `libc6.so` (glibc, the C compiler used in most Linux kernels)
- Since Nix derivations are immutable, upgrading a core library like `glibc` means recompiling all applications, because the glibc path to the nix store has been hardcoded
	- this means theres no concept of upgrading/downgrading - with Nix, you switch to another stack of dependencies


## Components
##### `/nix/store`
- initially contains the necessary software required to bootstrap a Nix system:
	- `bash`
	- `coreutils`
	- C compiler toolchain
	- `sqlite`
	- perl libraries
	- Nix itself + `libnix`
- can contain directories or files, alwas in the form of hash-name
- **NEVER CHANGE `/nix/store` MANUALLY**

##### Nix Database
- sqlite database that keeps track of the dependencies between derivations
	- stored under `/nix/var/nix/db`
	- schema is a simple table of valid paths, mapping from an auto-incremented `int` to a store path
	- you can inspect it by:
		- installing sqlite `nix-env -iA sqlite -f '<nixpkgs>'`
		- then running `sqlite3 /nix/var/nix/db/db.sqlite`

##### `/.nix-profile`
- general and convenient concept for realizing rollbacks
- profiles are used to compose components that are spread among multiple paths, under a new, unified path
- made up of multiple, versioned "generations"
	- whenever you change a profile, a new generation is created
	- generations can be switched & rolled back atomically, which makes them convenient for managing changes to your system
- inside the profile:
	- `nix-{version number}` is nix itself, with binaries & libraries
		- contains symbolic links that reproduce the hierarchy of the `nix-{version number}` store derivation
- `~/.nix-profile` is itself a symlink to `/nix/var/nix/profiles/default`
	- `default` is in turn a symlink to `default-1-link` in the same directory (the 1st generation of the `default` profile)
		- `default-1-link` is a symlink to the nix store "user-environment" derivation

##### Nixpkgs expressions
- **Nix expressions are used to describe packages & how to build them**
- channels are a set of packages and expressions available for download (stable & unstable)
	- `~/.nix-defexpr/channels` --> `/nix/var/nix/profiles/per-user/nix/channels` --> Nix store directory conaining the downloaded Nix expressions
- installer modifies `~/.profile` to automatically enter the Nix environment
	- What `~/.nix-profile/etc/profile.d/nix.sh` really does is simply to add `~/.nix-profile/bin` to `PATH` and `~/.nix-defexpr/channels/nixpkgs` to `NIX_PATH`

## Nix environment
- `nix shell hello`
	- installs software as a user, only for the Nix user
	- creates a new user environment - that's a new generation of our Nix user profile
	- nix manages environments, profiles, and their generations
	- list generations without stepping through the `/nix` hierarchy with `nix profile history`
	- list installed derivations with `nix profile list` 
		- derivation paths are the **output** of a build
	- after installing a package, use `nix profile rollback` to return to previous state
#### Closures
-  the closure of a derivation is a list of all its dependencies, recursively, including absolutely everything necessary to use that derivation
- copying all those derivations to the nix store of another machine makes you able to run it out of the box on the other machine
	- that's the base of deployment using Nix
	- useful when deploying software in the cloud
	- `nix-copy-closures` and `nix-store --export`
- see all dependencies in user profile with `nix-store -q --tree ~/.nix-profile`
- 