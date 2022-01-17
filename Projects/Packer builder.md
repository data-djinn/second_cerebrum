[[hashistack]] [[HCL Configuration Language]] [[Packer]]

==Defines what builders are started, how to provision them & what to do with their artifacts using `post-process` if necessary==

either:
- set the `sources` array of string with references to pre-defined sources
- define build-level `source` blocks 
    - this allows you to set specific fields

```
# build.pkr.hcl
build {
    # use the `name` field to name a build in the logs.
    # For example this present config will display
    # "buildname.amazon-ebs.example-1" and "buildname.amazon-ebs.example-2"
    name = "buildname"

    sources = [
        # use the optional plural `sources` list to simply use a `source`
        # without changing any field.
        "source.amazon-ebs.example-1",
    ]

    source "source.amazon-ebs.example-2" {
        # Use the singular `source` block set specific fields.
        # Note that fields cannot be overwritten, in other words, you cannot
        # set the 'output' field from the top-level source block and here.
        output = "different value"
        name = "differentname"
    }

    provisioner "shell" {
        scripts = fileset(".", "scripts/{install,secure}.sh")
    }

    post-processor "shell-local" {
        inline = ["echo Hello World from ${source.type}.${source.name}"]
    }
}
```

