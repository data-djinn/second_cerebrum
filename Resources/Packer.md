[[Projects/DevOps]]
### What is packer?
==packer builds machine images==
- machine images are resources that store all configurations, permissions, and metadata needed to create a virtual machine on your platform of choice 
- leverage what you know:
    - platform/cloud provider
    - Config management (chef, puppet, etc.)
    - Operating system/distros
- can build with JSON or HCL2

### Why use packer?
- **create images via template**
    - no need to spin up a VM and make changes yourself
    - Describe your desired machine image, and let packer generate it
- **automate all the things!**
    - packer fits nicely with existing CI/CD pipelines
    - use it to automatically test new config management formulas, or push approved builds to prod
- **Create identical & idempotent images**
    - cross-cloud, cross-environment, cross-platform
    - a single packer template can create machine images for multiple use cases
    - removes overhead of needing to individually configure machine images
```
packer {
  required_plugins {
    docker = {
      version = ">= 0.0.7"
      source = "github.com/hashicorp/docker"
    }
  }
}

source "docker" "ubuntu" {
  image  = "ubuntu:xenial"
  commit = true
}

build {
  name    = "learn-packer"
  sources = [
    "source.docker.ubuntu"
  ]
}
```
Packer Block
	- Contains Packer settings, including required version
	- Required plugins block contains all plugins required by the template to build your image
		- Anyone can write and use plugins
	- The source attribute is only necessary when requiring a plugin outside the HashiCorp domain. You can find the full list of HashiCorp and community maintained builders plugins in the Packer Builders documentation page.
	- The version attribute is optional, but we recommend using it to constrain the plugin version so that Packer does not install a version of the plugin that does not work with your template. If you do not specify a plugin version, Packer will automatically download the most recent version during initialization.

#### Source block
- Congifures a specific builder plugin, which is subsequently invoked by build block
- Use builders and communicators to define what kind of virtualization to use, how to launch the image you want to provision, and how to connect to it
- Source can be reused across multiple builds,
  - Use mutiple sources in a asingle build
  - Builder plugin is responsible for creating a machine and turning that machine into an image

- Source block:
		 Builder type "docker"
		- Name "ubuntu"
	- Each builder has its own unique set of configuration attributes
	- Docker builder starts a docker container , runs provisioners within the container, and then exports the container for reuse or commits the image

##### Build block
- Defines what packer should do with the docker container after it launches

Provision block
## Post-process blocko

`$ packer init .`
Installed plugin github.com/hashicorp/docker v0.0.7 in "/Users/youruser/.packer.d/plugins/github.com/hashicorp/docker/packer-plugin-docker_v0.0.7_x5.0_darwin_amd64"

`$ packer fmt .`
`docker-ubuntu.pkr.hcl`

`$ packer validate .`
Returns nothing, config is valid!

`$ packer build {image_name}`