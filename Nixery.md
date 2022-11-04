[[Nix]] [[Docker]]

Status quo of containers: *stateful* Dockerfiles

what would a Dockerfile look like if we *didn't have to explicitly build images for cluster deployment?*
something like:
```yaml
apiVersion: k8s.nixos.org/v1
kind: NixImage
metadata:
	name: curl-and-jq
data:
	tag: v1
	contents:
		- curl
		- jq
		- bash
```

*what if we didn't need to make yaml for Kubernetes?*
`nixery.dev/shell/curl/jq:v1` - that's it!!
- just references keys from `nixpkgs`

### Nixery
- tracks latest nix channel
- supports multiple methods for importing package sets into Nixery
	- `NIXERY_PKGS_PATH` local path env var
	- GitHub repository
##### Nixery image tags
- if using public nixpkgs repository:
	- reference specific channels/commits
- if using private repository:
	- CI can substitute these as parameters
	- use as part of your deployment strategy