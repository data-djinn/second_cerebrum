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
	- 