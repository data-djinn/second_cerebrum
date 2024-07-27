[[Nix]]
==Derivations (bulid tasks) are the building blocks of a Nix system, from a file system view point==

# The derivation function
- a derivation from Nix's view point is simply a set with some attributes
- so, you can pass the derivation around with variables like anything else
- `builtins.derivation` function is used to describe a single derivation
	- takes a set as input, with the following attributes:
		- **`system`: string identifying a Nix system type**
			- `i686-linux`/`darwin`
			- `x86_64-linux`/`darwin
			- `aarm64-linux`/`darwin`
			- run `nix -vv --version` to view current system type
			- the build can only be performed on a machine & operating system matching the system type
		- **`name`: string used as a symbolic name for the package by `nix-env`**
			- appended to the output paths of the derivation
		- **`builder`: identifies the program that is executed to perform the build**
			- can either be a derivation itself, or a source (e.g. `./builder.sh`)
	- every attribute is passed as an environment variable to the builder:
		- strings & nums are passed in verbatim
		- filepaths cause the referenced file to be copied to the store; its location is put in the environment variable
		- derivations are built prior to the current variable
		- lists of the previous types are also permitted
		- `true` is passed as the string `1`, `false` & `null` are passed as an empty string
	- optional attribute `args` specifies command-line arguments to be passed to the builder (should be a list)
	 - optional attribute `outputs` specifies a list of symbolic outputs of the derivation
		 - by default, a derivation produces a single output path `out`
		 - they can produce multiple output paths however
			 - this is useful because it allows outputs to be downloaded or garbage-collected separately
			 - e.g. if a library provides a library, header files, & documentation, you can reference each independently
				 - `outputs = ["lib" "headers" "doc" ]`
				- nix will then pass those 3 values 

## `mkDerivation`
- wrapper around `derivation` that **adds a default value for system & always uses bash as the builder**

## Builder execution
- temporary directory is created under the directory specified by `TMPDIR` (default `/tmp`), where the build takes place, then `cd`s to that dir
- environment is cleared & set to the derivation attributes, as specified above
- the following variables are set:
	- `NIX_BUILD_TOP` contains the path of the temporary directory for this build
	- `TMPDIR`, `TEMPDIR`, `TMP`, `TEMP` are set to point to the temporary directory
	- `PATH` is set to `/path-not-set` to prevent shells from initialising it
	- `HOME` is set to `/homeless-shelter` to prevent programs from using `etc/passwd` to find the user's home directory, which would cause impurity
	- `NIX_STORE` is set to the path of the top-level Nix store directory (typically, `/nix/store`)
	 - for each output declared in `outputs`, the corresponding environment variable is set to point to the intended path in the Nix store for that output
		 - eatch output path is a concatenation of the cryptogrophic hash of all build inputs, the `name` attribute, and the output name
 - if an output path already exists, it is removed
 - locks are acquired to prevent multiple Nix instances from performing the same build at the same time
 - a log of the combined standard output and error is written to `/nix/var/log/nix`
 - a builder is executed with the arguments specified by the `args` attribute
	 - if exits with exit code `0`, it is considered a success
 - temp directory is removed (unless `-K` flag was specified)
 - if the build was successful, Nix scans each output path for references to input paths by looking for the hash parts of the input paths
	 - since these are potential runtime dependencies, nix registers them as dependencies of the output paths
 - after the build, Nix:
	 - sets the last-modified timestamp on all files in the build result to 1 (00:00:01 1/1/1970 UTC)
	 - sets the group to the default group
	 - sets the file permissions to `0444` or `0555` (read-only, with execute permissions enabled if the file was originally executable)
		  - `setuid` and `setgid` bits are cleared (not supported by Nix)

# `.drv` files
`buildits.currentSystem` -> `"x86_64-linux"`
`d = derivation { name = "myname"; builder = "mybuilder"; system = "mysystem"; }`
`d` -> `<<derivation /nix/store/...-myname.drv`
- nix repl does not build derivations unless explicitly told to (with `:b my_example_drv` command, but it will create **`.drv` file**: ==Specification of how to build the derivation, without all the nix language fuzz==
`nix show-derivation /nix/store/z3hhlxbckx4g3n9sw91nnvlkjvyw754p-myname.drv` -> (below)
```
{
  "/nix/store/z3hhlxbckx4g3n9sw91nnvlkjvyw754p-myname.drv": {
    "outputs": {
      "out": {
        "path": "/nix/store/40s0qmrfb45vlh6610rk29ym318dswdr-myname"
      }
    },
    "inputSrcs": [],
    "inputDrvs": {},
    "platform": "mysystem",
    "builder": "mybuilder",
    "args": [],
    "env": {
      "builder": "mybuilder",
      "name": "myname",
      "out": "/nix/store/40s0qmrfb45vlh6610rk29ym318dswdr-myname",
      "system": "mysystem"
    }
  }
}
```
1. output paths 
	- there can be multiple 
	- nix creates one by default (`"out"`)
1. the list of input derivations 
	- empty because we are not referring to any other derivation
	 - otherwise it would be a list of other `.drv` files
 3. system and builder executable
 4. list of environment variables passed to the builder

```nix-repl> builtins.isAttrs d
true
nix-repl> builtins.attrNames d
[ "all" "builder" "drvAttrs" "drvPath" "name" "out" "outPath" "outputName" "system" "type" ]
```
- out path does not exist yet
- hash of the out path is based solely on the input derivations in the current version of Nix, not the contents of the build product
	- it is possible, however, to have content-addressable derivations for e.g. tarballs

## referring to other derivations with `outPath`
- `outPath` describes the location of the files of that derivation
- can convert from derivation set to a string
```
nix-repl> d.outPath
"/nix/store/40s0qmrfb45vlh6610rk29ym318dswdr-myname"
nix-repl> builtins.toString d
"/nix/store/40s0qmrfb45vlh6610rk29ym318dswdr-myname"
```

## When the `.drv` is built?
- nix does not build derivations during evaluation of nix expressions
	- that's why you need to use the `:b` command
- **evaluation time** is when the nix expression is parsed, interpreted, and finally returns a derivation set
	- during evaluation, you can refer to other derivations because Nix will create `.drv` files and we will know out paths beforehand with `nix-instantiate`
- **build time**:  the `.drv` from the derivation set is built, first from building `.drv` inputs with `nix-store -r`
in summary, when nix builds a derivation, it first recursively creates `.drv` files from a derivation expression, then uses them to build the output

### Packaging a simple C program
```
void main() {
  puts("Simple!");
}
```
and its `simple_builder.sh`:
```
export PATH="$coreutils/bin:$gcc/bin"
mkdir $out
gcc -o $out/simple $src
```


```
nix-repl> :l <nixpkgs>
> simple = derivation { name = "simple"; builder = "${bash}/bin/bash"; args = [ ./simple_builder.sh ]; gcc = gcc; coreutils = coreutils; src = ./simple.c; system = builtins.currentSystem; }
> :b simple
this derivation produced the folliwing outputs
out -> /nix/store/ni66p4jfqksbmsl616llx3fbs1d232d4-simple
```
^2 attributes added: `gcc` & `coreutils`. Name on the left is the key in the derivation set, name on the right refers to the derivation from nixpkgs

Forget the repl, we really want a `simple.nix` file:
```
let
  pkgs = import <nixpkgs> {};
in
  pkgs.stdenv.mkDerivation {
    name = "simple";
    builder = "${pkgs.bash}/bin/bash";
    args = [ ./simple_builder.sh ];
    # can also use: inherit (pkgs) gcc coreutils
    gcc = pkgs.gcc;
    coreutils = pkgs.coreutils;
    src = ./simple.c;
    system = builtins.currentSystem;
}
```
- build with `nix-build simple.nix`
	- this creates a symlink `result` in the current directory, pointing to the outpath of the derivation
	- `nix-build` does 2 jobs:
		- `nix-instantiate` parses and evaluates `simple.nix`, returning a `.drv` file corresponding to the parsed derivation set
		- `nix-store -r` builds the .drv file

# Generic builders
- what if we want to package up `autoutils` dependencies along with our simple.nix?
- one way would be to simply inherit all dependencies explicitly:
`hello.nix`:
```
let
  pkgs = import <nixpkgs> {};
in
  derivation {
    name = "hello";
    builder = "${pkgs.bash}/bin/bash";
    args = [ ./hello_builder.sh ];
    inherit (pkgs) gnutar gzip gnumake gcc coreutils gawk gnused gnugrep;
    bintools = pkgs.binutils.bintools;
    src = ./hello-2.12.1.tar.gz;
    system = builtins.currentSystem;
  }
```
but this requires us to pass all dependencies every time. lets make this more DRY:

`autotools.nix`:
```
pkgs: attrs:
  let defaultAttrs = {
    builder = "${pkgs.bash}/bin/bash";
    args = [ ./builder.sh ];
    baseInputs = with pkgs; [ gnutar gzip gnumake gcc coreutils gawk gnused gnugrep binutils.bintools ];
    buildInputs = [];
    system = builtins.currentSystem;
  };
  in
  derivation (defaultAttrs // attrs)  # union of two sets
```
- returns a function that:
	- first drops into the scope of the `pkgs` attribute set
	- defines a helper variable `defaultAttrs` that serves as a set of common attributes to be used in downstream derivations
	- creates the derivation with the union of all attributes
then rewrite `hello.nix` like so:
```
let
  pkgs = import <nixpkgs> {};
  mkDerivation = import ./autotools.nix pkgs;
in 
  mkDerivation {
    name = "hello";
    src = ./hello-2.12.1.tar.gz;
  }
```

*In C, you create objects in the heap, then you compose them inside new objects. Pointers are used to refer to other objects*
*In nix, you create derivations in the nix store, then compose them by creating new derivations. Store paths are used to point to other derivations*

**Nix is able to compute all runtime dependencies automatically for us**
- not only shared libraries, but also referenced executables, scripts, python libraries, etcs
- this makes nix packages self-contained, ensuring that copying the runtime closures on another machine is sufficient to run the program
# `nix-shell`
- drops us in a shell by setting up the necessary environment variables to hack on a derivation
- it does not build the derivation, it only serves as preparation so we can run the build
```
$ nix-shell hello.nix
[nix-shell]$ make
bash: make: command not found
[nix-shell]$ echo $baseInputs
/nix/store/jff4a6zqi0yrladx3kwy4v6844s3swpc-gnutar-1.27.1 [...]
 [nix-shell]$ source builder.sh
 [nix-shell]$ cd hello-2.10
 [nix-shell]$ make
 ...
```
- the builder script runs all the steps including setting up `$PATH`

# Inputs design pattern
### Repositories in nix
### Monorepo
- e.g. nixpkgs
- create a top-level Nix expression, and one expression for each package
- the top level expression imports & combines all expressions in a giant attribute set with name -> package pairs
	- because nix is lazy, it only evaluates what's needed

- nix does not enforce any particular repo format