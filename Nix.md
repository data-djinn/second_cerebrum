[[Haskell]] [[DevOps]]

# Intro
## Purely functional package manager
- treats packages like values in Haskell
	- no side-effects
	- never change after they've been built
- packages stored in ==Nix store==, usually in `/nix/store`
	- each package has its own unique subdirectory
	- each package has a hash of all its build dependencies
## Multiple versions
- you can have *multiple versions* or variants of a package installed at the same time
	- this is especially important when different applications have dependencies on different versions of the same package
	- different versions reside in different hashed paths in the Nix store
- this means that operations like upgrading or uninstalling an application cannot break other applications, since these operations never "destructively" update or delete files that are used by other packages (dependency graph isolation)
## Complete dependencies
- Since Nix doesn't install packages *globally* (i.e. in `/usr/bin`), but in package-specific directories, the risk of incomplete dependencies is greatly reduced
	- other package managers won't complain if the package isn't specified, but available in the current user's global packages during build time
	- this makes it unreliable to share with other would-be users

## Multi-user support
- non-privileged users can securely install software
- each user can have a different *profile*: ==a set of packages in `/nix/store` that appear in the user's `PATH`==
	- if a user installs a package that another user has already installed, the package won't be built or downloaded a second time
	- at the same time, it's not possible for one user to inject a Trojan horse into a package that might be used by another user

## Atomic upgrades & rollbacks
- package management operations are *atomic*
	- they never overwrite packages in `/nix/store`, but just add new versions in different paths
	- **the old versions are still there after an upgrade**
- `nix-env --upgrade -A nixpkgs.some-package`
- rollback with `nix-env --rollback`

## Garbage Collection
- `nix-env --uninstall firefox` does *not* delete the package
	- you may want to `--rollback`, or it may be in the profiles of other users
- delete unused packages safely by running `nix-collect-garbage`
	- deletes all packages that aren't in use by any user profile or by a currently running program

## Functional language
- Nix expressions describe everything that goes into a package build action ("derivation"):
	- other packages
	- sources
	- build scripts
	- env variables
	- etc.
- Nix expressions are *deterministic*: building a nix expression twice should yield the same result
- easy to build multiple variants of a package:
	- turn the nix expression into a function and call it any number of times with the appropriate args
	- variants won't conflict with each other in `/nix/store` due to the hashing scheme

## source/binary deployment
- nix expressions describe how to build a package from source
- usually, nix will skip building from source & instead use a *binary cache stored in `cache.nixos.org`*

## Managing build environments
- Nix makes it easy to automatically set up the build environment for a package
- given a nix expression that describes the dependencies of your package, the command `nix-shell` builds/downloads all those dependencies (if they're not already in the Nix store), then start a Bash shell in which all necessary environment variables are set
- 