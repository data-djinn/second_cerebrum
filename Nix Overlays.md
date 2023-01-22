[[Nix]]
==extend & change nixpkgs==
- functions that accept 2 arguments:  self & super; and return a set of packages
	- self = final, super = previous
	- similar to `packageOverrides`
	- same concept as a subclass wrt overriding & calling methods
![[Pasted image 20230121102447.png]]

```
final: prev: { f = prev.firefox; }  # more comprehensible than self: super

final: prev: {
	firefox = prev.firefox.override { ... };
	myBrowser = final.firefox;
}

final: prev: firefox = final.firefox.override { ... };  # ERROR! infinite recursion
```

## a

