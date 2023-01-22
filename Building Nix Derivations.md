[[Nix]]
==Derivations (bulid tasks) are the building blocks of a Nix system, from a file system view point==

# The derivation function
- a derivation from Nix's view point is simply a set with some attributes
- so, you can pass the derivation around with variables like anything else
- `builtins.derivation` function is used to describe a single derivation
	- takes a set as input, with the following attributes:
		- `system`: string identifying a Nix system type
			- `i686-linux`/`darwin`
			- `x86_64-linux`/`darwin
			- `aarm64-linux`/`darwin`
			- run `nix -vv --version` to view current system type
			- the build can only be performed on a machine & operating system matching the system type
		- `name`: string used as a symbolic name for the package by `nix-env`
			- appended to the output paths of the derivation
		- `builder`: indentifies the program that is executed to perform the build
			- can either be a derivation itself, or a source (e.g. `./builder.sh`)
	- every attribute is passed as an environment variable to the builder:
		- strings & nums are passed in verbatim
		- filepaths cause the referenced file to be copied to the store; its location is put in the evironment variable
		- derivations are built prior to the current variable
		- lists of the previous types are also permitted
		- `true` is passed as the string `1`, `false` & `null` are passed as an empty string
	- optional attribute `args` specifies command-line arguments to be passed to the bulider (should be a list)
	 - optional attribute `outputs` specifies a list of symbolic outputs of the derivation
		 - by default, a derivation produces a single output path `out`
		 - they can produce multiple output paths however
			 - this is usefull because it allows outputs to be downloaded or garbage-collected seperately
			 - e.g. if a library provides a library, header files, & documentation, you can reference each independently
				 - `outputs = ["lib" "headers" "doc" ]`
				- nix will then pass those 3 values 

## `mkDerivation`
- wrapper around `derivation` that **adds a defailt value for system & always uses bash as the builder**

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
 - after the build, NixQ
	 - sets the last-modified timestamp on all files in the build result to 1 (00:00:01 1/1/1970 UTC)
	 - sets the group to the default group
	 - sets the file permissions to `0444` or `0555` (read-only, with execute permisions enabled if the file was originally executable)
		  - `setuid` and `setgid` bits are cleared (not supported by Nix)

# `.drv` files
`buildits.currentSystem` -> `"x86_64-linux"`
`d = derivation { name = "myname"; builder = "mybuilder"; system = "mysystem"; }`
`d` -> `<<derivation /nix/store/...-myname.drv`
- nix repl does not build derivations unless explicitly told to, but it will create `.drv` file
==Specification of how to build the derivation, without all the nix language fuzz==
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
1. ouptut paths 
	- there can be multiple 
	- nix creates one by default ("out")))
1. the list of input derivations 
	- empty because we are not referring to any other derivation
	 - otherwise it would be a list of other `.drv` files
 3. system and builder executable
 4. list of environment variables passed to the builder

- out path does not exist yet
- hash of the out path is based solely on the input derivations in the current version of Nix, not the contents of the build product
	- it is possible, however, to have content-addressable derivations for e.g. tarballs

# refering to other derivations
- 