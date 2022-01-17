[[hashistack]] [[Cloud]]

```
source "amazon-ebs" "main" {
  ami_name = "main-ami"
}

<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" {
  # Block body
  <IDENTIFIER> = <EXPRESSION> # Argument
}
```
- the ordering of root blocks is not significant, except `provisioner` & `post-processor`
- also can use .json, but certain features are only available with HCL