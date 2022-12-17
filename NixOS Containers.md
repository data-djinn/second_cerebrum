[[Nix]] [[DevOps]]

**==Easily run other NixOS instances as containers ==**
- NixOS containers share the Nix store of the host, making container creation very efficient
- 2 ways to create:
1. imperatively with `nixos-container` command
2. declaratively with `configration.nix`
	- your containers will get upgraded along with your host system when you run `nixos-rebuild`

### Imperative Container management
- only possible as root
- `nixos-container create foo`