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
- the standard `checks` output specifices  a set of derivaions to be built by a continuous integration system such as hydra
- because flake evaluation *is* hermetic and the lock file locks all depndencies, it's **guaranteed** that the `nix build` command above will evaluate to the same result as the one in the CI system


### The flake registry
- flake locations are specified using a URL-like syntax such as `github:edolstra/dwarffs` or`git+https://github.com/NixOS/patchelf`
- because such URLs would be verbose if you had to type them all the time on the comand line, there is also a [flake registry](https://raw.githubusercontent.com/NixOS/flake-registry/master/flake-registry.json)
	- maps symbolic identifiers such as `nixpkgs` to actual locations like `https://github.com/NixOS/nixpkgs`
	- so these are equivalent:
```shell
$ nix shell nixpkgs#cowsay --command cowsay Hi!
$ nix shell github:NixOS/nixpkgs#cowsay --command cowsay Hi!
```
- to override the registry locally, add a new registry to overwrite the remote registry
	- `nix registry add nixpkgs ~/my-nixpkgs`
- or pin it to a specific revision:
	- `nix registry add nixpkgs github:NixOS/nixpkgs/5272327b81ed355bbed5659b8d303cf2979b6953`

## Creating a flake
- just add a `flake.nix` to your project's repository
```nix
{
  description = "A flake for building Hello World";

  inputs.nixpkgs.url = github:NixOS/nixpkgs/nixos-20.03;

  outputs = { self, nixpkgs }: {

    defaultPackage.x86_64-linux =
      # Notice the reference to nixpkgs here.
      with import nixpkgs { system = "x86_64-linux"; };
      stdenv.mkDerivation {
        name = "hello";
        src = self;
        buildPhase = "gcc -o hello ./hello.c";
        installPhase = "mkdir -p $out/bin; install -t $out/bin hello";
      };

  };
}
```
- `description` attribute is a one-line description shown by `nix flake metadata`
- `inputs` attribute specifies other flakes that this flake depends on
	- these are fetched by Nix & passed as arguments to the `outputs` function
- **the `outputs` attribute is the heart of the flake**
	- it's a function that produces an attribute set
	- the function ags are the flakes specified in `inputs`
	- the `self` argument denotes *this flake*
		- it's primarily useful for referring to the source of the flake (`src = self;`),
		- or to other outputs, e.g. `self.defaultPackage.x86_64-linux`
- attributes produced by `outputs` are arbitrary values, except that (as we saw above) there are some standard outputs such as `defaultPackage.${system}`
- every flake has some metadata, such as `self.lastModifiedDate`, which is used to generate a version string like `hello-20221104`
### building a flake
- the command `nix flake init` creates a basic `flake.nix` for you
- **any file that is not tracked by Git is invisible during Nix evaluation**
	- this is to ensure hermetic evaluation
	- so `git add flake.nix`
- build our new flake with `nix build`
- You can also get an interactive development environment in which all dependencies (like gcc) and shell variables & functions from the derivation are in scope:
```nix
nix develop
eval "$buildPhase"
./hello
---------
Hello World
```

 - referring to `github:NixOS/nixpkgs/nixos-20.03` is imprecise as it refers to a branch, but not a specific revision
	- `nix build` generates a lock file that precisely states which revision of `nixpkgs` to use
	- every subsequent build of this flake will use the version of `nixpkgs` recorded in the lock file
	- if you add new inputs to the flake, Nix will automatically add corresponding locks to `flake.lock`
		- it won't replace existing locks
- if you want to update a locked input to the latest version:
`nix flake lock --update-input nixpkgs && nix build`
make sure nix flake is correct: `nix flake check`
```shell
nix flake check 
echo "assuming it looks good"
git commit -a -m 'Initial version'
git remote add origin git@github.com:data-djinn/hello.git
git push -u origin main
```

now other users can use this flake!
`nix shell github:data-djinn/hello -c hello`


## Evaluating caching
#### Nix evaluation is often slow
- Nix uses a simple, interpreted, purely functional language to describe packages dependency graphs & NixOS system configurations
- to get any information about those things, Nix first needs to *evaluate* a substancial Nix program
	- this involves parsing potentially thousands of `.nix` files & running a Turing-complete langage
	- e.g. the command `nix-env -qa` shows you which packages are available in Nixpgks
		- this is slow & consumes a lot of memory:
```shell
$ command time nix-env -qa | wc -l
-----------------------------------
5.09user 0.49system 0:05.59elapsed 99%CPU (0avgtext+0avgdata 1522792maxresident)k
28012
```
- evaluating individual packages or configurations can also be slow
	- e.g. using `nix-shell` to enter a dev enviroment for [Hydra](https://github.com/NixOS/hydra) takes a while, even if all dependencies are present in the Nix store:
```shell
$ command time nix-shell --command 'exit 0'
-----------------------------------
1.34user 0.18system 0:01.69elapsed 89%CPU (0avgtext+0avgdata 434808maxresident)k
```
- this may be fine on occasion, but unacceptably slow in production-critical scripts
	- note that the evaluation overhead is completely independent from the time it takes to actually build or download a package or configuration
	- if a dependency is already present in the Nix store, Nix won't build or download it again
	- however it will still need to re-evaluate the Nix files to determine which Nix store paths are needed

### Solution: Cache evaluation results
- because Nix is purely functional, re-evaluation should produce the same result every time
	- this didn't work in the past because Nix evaluation was not *hermetic*
	- for example, a `.nix` file can import other Nix files through relative or absolute paths (such as `~./config/nixpkgs/config.nix` or `$NIX_PATH`) - this can be inconsistent across machines

- one advantage of flakes' evaluation model is their **ability to cache evaluation results reliably** via **fully hermetic evaluation**
	- when you evaluate an output attribute of a particular flake (e.g. the attribute `defaultPackage.x86_64-linux`), Nix **disallows access to any files outside that flake or its dependencies**
	- it also disallows impure or platform-dependent features such as access to environment variables or the current system type
	- thus the `nix` command can aggressively cache evaluation results without fear of cache invalidation problems
- the first evaluation of a flake, nix will still need to evaluate the entire dependency graph of Firefox
```shell
$ command time nix shell nixpkgs#firefox -c firefox --version Mozilla Firefox 75.0 0.26user 
--------------------------------
0.05system 0:00.39elapsed 82%CPU (0avgtext+0avgdata 115224maxresident)k
```
- subsequent evaluations, however, are almost instantaneous (& takes up less memory):
```shell
$ command time nix shell nixpkgs#firefox -c firefox --version
Mozilla Firefox 75.0
0.01user 0.01system 0:00.03elapsed 93%CPU (0avgtext+0avgdata 25840maxresident)k
```
- the cache utilizes a sqlite database that stores the values of flake output attributes
`sqlite3 ~/.cache/nix/eval-cache-v1/302043eedfbce13ecd8169612849f6ce789c26365c9aa0e6cfd3a772d746e3ba.sqlite .dump`
```sql
PRAGMA foreign_keys=OFF;
BEGIN TRANSACTION;
CREATE TABLE Attributes (
    parent      integer not null,
    name        text,
    type        integer not null,
    value       text,
    primary key (parent, name)
);
INSERT INTO Attributes VALUES(0,'',0,NULL);
INSERT INTO Attributes VALUES(1,'packages',3,NULL);
INSERT INTO Attributes VALUES(1,'legacyPackages',0,NULL);
INSERT INTO Attributes VALUES(3,'x86_64-linux',0,NULL);
INSERT INTO Attributes VALUES(4,'firefox',0,NULL);
INSERT INTO Attributes VALUES(5,'type',2,'derivation');
INSERT INTO Attributes VALUES(5,'drvPath',2,'/nix/store/7mz8pkgpl24wyab8nny0zclvca7ki2m8-firefox-75.0.drv');
INSERT INTO Attributes VALUES(5,'outPath',2,'/nix/store/5x1i2gp8k95f2mihd6aj61b5lydpz5dy-firefox-75.0');
INSERT INTO Attributes VALUES(5,'outputName',2,'out');
COMMIT;
```
- in other words, the cache stores all the attributes that `nix shell` had to evaluate, in particular `legacyPackages.x86_64-linux.firefox.{type, drvPath, outPath, outputName}`
	- it also stores negative lookups, that is, attributes that don't exist (e.g. `packages`)
- the name of the sqlight database, `302043eedf`...`.sqlite`, is derived from the contents of the top-level flake
	- since the flake's lock file contains content hashes of all dependencies, this is enough to efficiently and completely capture all files that might influence the evaluatian result
- the `nix search` command uses the new evaluation cache instead of the previous ad-hoc cache
first time:
```shell-session
$ command time nix search nixpkgs blender
* legacyPackages.x86_64-linux.blender (2.82a)
  3D Creation/Animation/Publishing System
5.55user 0.63system 0:06.17elapsed 100%CPU (0avgtext+0avgdata 1491912maxresident)k
```
subsequent searches:
```shell-session
$ command time nix search nixpkgs blender
* legacyPackages.x86_64-linux.blender (2.82a)
  3D Creation/Animation/Publishing System
0.41user 0.00system 0:00.42elapsed 99%CPU (0avgtext+0avgdata 21100maxresident)k
```
- the only way cached results can become "stale" is by garbage collection

## Managing NixOS systems
#### What problems do flakes solve for NixOS systems?
##### Lack of reproducibility
- NixOS *should* always get the same system for each build (except mutable state, such as the contents of a database)
	- however, the default NixOS workflow doesn't provide reproducible system configurations out of the box

consider this typcial sequence of commands to initialize a nixos system:
1. you edit `etc/nixos/configuration.nix`
2. you run `nix-channel --update` to get the latest version of the `nixpkgs` repo
3. you run `nixos-rebuild switch`, which evaluates & builds a function in the `nixpkgs` repository that takes `/etc/nixos/configuration.nix` as an input
- in this workflow, `configuration.nix` may not be under configuration management (e.g. pointing to a Git repository)
- if it is, it might be using a dirty working tree
- furthermore, `configuration.nix` doesn't specify what git revision of `nixpkgs` to use
	- if somebody else deploys the same `configuration.nix`, they might get widely different package versions

##### Lack of traceability
- you can't tell what configuration you're actually running
	- from a running system, you should be ale to get back to it's specification
	- we want the ability to trace derived artifacts back to their sources
- NixOS currently doesn't have very good traceability
	- you can ask a NixOS system what version of Nixpkgs it was built from:
		- `nixos-version --json | jq -r .nixpkgsRevision`
	- unfortunately, this method doesn't allow you to recover `configuration.nix`, or any other external NixOS modules that were used by the configuration

##### Lack of composability
- although it's easy to enable a package or system service from the nixpkgs repository, we have to use side-effectful mechanisms like `$NIX_PATH` or `builtins.fetchGit`, or import via relative paths
	- these are not standardized, and are inconvenient to use
	- NixOS is currently built around a monorepo workflow
		- the entire universe of packages must be added to the `nixpkgs` repository, otherwise challenges arise
		- your `configuration.nix` file is not a part of `nixpkgs`, so the monorepo approach does not even apply to any standard NixOS deployment

### Use flakes for NixOS configuration!
- `flake.nix` files solve the above problems:
	- **reproducability**: the entire system configuration (including all dependencies) is captured by the flake & its lock file
		- if 2 peoplee check out teh same git revision of a flake & build it, they get the same result
	- **traceability**: `nixos-version` prints the git revision of the top-level configuration flake, not its `nixpgks` input
	- **composabilty**: it's easy to pull in packages and modules from other repositories as flake inputs

### Creating a NixOS configuration flake
```shell
$ git init my-flake
$ cd my-flake
$ nix flake init -t templates#simpleContainer
$ git commit -a -m 'Initial version'
```
- the `-t` flag for `nix flake init` specifies a *template* from which to copy the initial contents of the flake
- to see what templates are avaliable, run: `nix flake show templates`
```nix
{
  inputs.nixpkgs.url = "github:NixOS/nixpkgs/nixos-20.03";

  outputs = { self, nixpkgs }: {

    nixosConfigurations.container = nixpkgs.lib.nixosSystem {
      system = "x86_64-linux";
      modules =
        [ ({ pkgs, ... }: {
            boot.isContainer = true;

            # Let 'nixos-version --json' know about the Git revision
            # of this flake.
            system.configurationRevision = nixpkgs.lib.mkIf (self ? rev) self.rev;

            # Network configuration.
            networking.useDHCP = false;
            networking.firewall.allowedTCPPorts = [ 80 ];

            # Enable a web server.
            services.httpd = {
              enable = true;
              adminAddr = "morty@example.org";
            };
          })
        ];
    };

  };
}
```
- this flake has only 1 input (`nixpkgs` 20.03 branch) 
- & 1 output, `nixosConfigurations.container`, which evaluates a NixOS configuration for tools like `nixos-rebuild` and `nixos-container`
- the main argument is `modules`, which is a list of NixOS configuration modules
	- this takes the place of the `configuration.nix` in non-flake deployments
	- you can write `modules = [ ./configuration.nix ]` if you're converting a pre-flake NixOS configuration
- create & start the container!
`nixos-container create flake-test --flae /path/to/my-flake` --> `host IF is 10.233.4.1, container IP is 10.233.4.2`
`nixos-container start flake-test`
- to test if the container is working, let's connect to it:
`curl http://flake-test/`