[[HCL Configuration Language]] 

#### Artifacts
==the result of a single build, usually a set of IDs or files to represent a machine image==
- every builder produces a single artifact
- e.g. an AMI id for AWS EC2

#### Builds
==a single task that eventually produces an image for a single platform==
- multiple builds run in parallel
- e.g. "The Packer build produced an AMI to run our web application"

#### Builders
==components of packer that are able to create a machine image for a single platform==
- Builders are read in some configuration and use that to run and generate a machine image
- invoked as part of a build in order to create the actual resulting images

#### Commands
==sub-commands for the `packer` program that perform some job==

#### Data Sources
==components of Packer that fetch data from outside Packer and make it available to use within the template==
- e.g. AWS AMI / Secrets Manager

#### Post-processors
==components of packer that take the result of a builder or another post-processor and process that to create a new artifact==
- e.g. compress artifacts, upload, etc.

#### Provisioners
==components of Packer that install and configure software within a running machine prior to that machine being turned into a static image==
- provisioners perform the major work of making the image contain useful software
- e.g. shell scripts, Chef, Puppet, etc

#### Templates
==json files which define one or more builds by configuring the various components of Packer==
- Packer is able to read a template and use that information to create multiple machine images in parallel 