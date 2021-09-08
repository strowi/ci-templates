# Terraform

To ease work with terraform [here](../build/terraform.yml)
is a template you can use.

This will:

* fmt
  * terraform-fmt
  * [terraform-docs](https://terraform-docs.io/)
  * push back those changes to the repository
* [checkov](https://www.checkov.io/)
  * scan for misconfigurations
  * show those in MR-Widget / Code
* plan
  * show changes
* apply
  * apply changes

To ease working with different terraform versions,
[tfswitch](https://tfswitch.warrensbox.com/) is being
utilized in every step. This will download + cache
the `required_version` of terraform.

## Howto

```yaml
---
include:
  - project: 'strowi/ci-templates'
    ref: 'tfs'
    file: '/build/terraform.yml'
  - project: 'strowi/ci-templates'
    file: '/tests.yml'

stages:
  - generate
  - test
  - plan
  - apply

checkov:
  extends: .checkov
  stage: test
  parallel:
    matrix:
      - TF_ROOT:
        - "$TERRAFORM_ROOT1"
        - "$TERRAFORM_ROOT2"

fmt:
  stage: generate
  extends: .fmt
  variables:
    TF_ROOT_DIRS: $TERRAFORM_DIR1 $TERRAFORM_DIR2

plan:
  extends: .plan
  parallel:
    matrix:
      - TF_ROOT:
        - "$TERRAFORM_ROOT1"
        - "$TERRAFORM_ROOT2"

# Separate apply job for manual Terraform as it can be destructive action.
deploy:
  extends: .deploy
  parallel:
    matrix:
      - TF_ROOT:
        - "$TERRAFORM_ROOT1"
        - "$TERRAFORM_ROOT2"
```

