[[Nix]] [[DevOps]]
#### Improves
- reproducibility
- composability
- usability
in the nix ecosystem

### Why? What problems do flakes solve?
##### Historically:
- Nix pioneered reproducible builds
	- it tries hard to ensure that two builds of the same derivation graph produce an identical result (idempotency)
- evaluation of Nix *files* into such a derivation graph isn't nearly as reproducible
	- Nix files can access arbitrary files (such as `~/.config/nixpkgs/config.nix`), env vars, git repos, binaries in `$NIX_PATH`, cli arguments, and the system type (`builtins.currentSystem`)
	- so, *evaluation is not as hermetic as it could be*
- **No standard way to compose Nix-based projects**
	- it's rare that everything you need is in Nixpkgs
		- you may be using Nix as a build tool, or NixOS system configurations
	- Typical ways to compose Nix files are to rely on the Nix search path (e.g. `import <nixpkgs>`), or to use `fetchGit`/`fetchTarball`
	- former has poor reproducibility, while the latter provides a bad user experience because of the need to manually update Git hashes to update dependencies
- There's also no easy way to *deliver* Nix-based projects to users
- Nix has a "channel" mechanism (essentially a tarball containing Nix files), but it's not easy to create channels & they are not composable
- Nix-based projects lack a standardized structure

## ==flakes are a solution to these problems==
- a flake is **simply a source tree (like a git repo) containing a file named `flake.nix` that provides a standardized interface to Nix artifacts such as packages or NixOS modules**
- flakes can have dependencies on other flakes, with a "lock file" pinning those dependencies to exact revisions to ensure reproducible evaluation (like `Pipfile.lock`)
	- [Original RFC here](https://github.com/NixOS/rfcs/pull/49)
- in the past, you might have used the `NIX_PATH` env var to allow your project to find Nixpkgs
	- in the world of flakes, this is no longer allowed
	- flakes have to declare their dependencies explicitly, and these dependencies have to be *locked* to specific revisions
	- in order to install a flake, `flake.nix` declares an explicit dependency on Nixpkgs, which is also a [flake](https://github.com/NixOS/nixpkgs/blob/master/flake.nix)
		- so your new flake depends on a *specific version* of the `nixpkgs` & `nix` flakes
			- we don't need to specify the versions; instead it's automatically recorded in `flake.lock`
		- as a result, building `dwarffs` will **always produce the same result** :)

### Flake outputs
- another goal of flakes is to provide a standard structure for discoverability within Nix-based projects
- Flakes can provide arbitrary Nix values, such as:
	- packages
	- NixOS modules
	- Library functions
==...these are called its **outputs**==
- see the outputs of a flake with `nix flake show <flake>`
```shell
github:edolstra/dwarffs/d11b181af08bfda367ea5cf7fad103652dc0409f
├───checks
│   ├───aarch64-linux
│   │   └───build: derivation 'dwarffs-0.1.20200409'
│   ├───i686-linux
│   │   └───build: derivation 'dwarffs-0.1.20200409'
│   └───x86_64-linux
│       └───build: derivation 'dwarffs-0.1.20200409'
├───defaultPackage
│   ├───aarch64-linux: package 'dwarffs-0.1.20200409'
│   ├───i686-linux: package 'dwarffs-0.1.20200409'
│   └───x86_64-linux: package 'dwarffs-0.1.20200409'
├───nixosModules
│   └───dwarffs: NixOS module
└───overlay: Nixpkgs overlay
```
- flakes can have arbitrary outputs
- **some of them have special meaning to certain Nix commands & therefore must have a specific type**
	- e.g. `defaultPackage.<system>` must be a derivation - it's what `nix build` and `nix shell` will build by default (unless you specify another output)
	- `nix` cli allows you to specify another output through a syntax similar to URL fragments:
	  `nix build github:edolstra/dwarffs#checks.aarch64-linux.build`
the standard c