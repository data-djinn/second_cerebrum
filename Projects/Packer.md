[[DevOps]]
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