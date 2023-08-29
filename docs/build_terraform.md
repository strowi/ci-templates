# Terraform

To ease work with terraform [here](../build/terraform.yml)
is a template you can use.

This will:

- fmt
  + `terraform format`
  + [terraform-docs](https://terraform-docs.io/)
  + push back those changes to the repository
    * requires `CI_SSH_PRIVATE_KEY` being set with push access
- [checkov](https://www.checkov.io/)
  + scan for misconfigurations
  + show those in MR-Widget / Code
- plan
  + show changes
- infracost
  + show cost estimation
  + requires `INFRACOST_API_KEY` (run `infracost register` to obtain)
- apply
  + apply changes

To ease working with different terraform versions,
[tfswitch](https://tfswitch.warrensbox.com/) is being
utilized in every step. This will download + cache
the `required_version` of terraform.

## Howto

```yaml
---

include:
  - project: 'strowi/ci-templates'
    file: '/build/terraform.yml'
  - project: 'strowi/ci-templates'
    file: '/tests.yml'

stages:
  - generate
  - test
  - plan
  - cost
  - apply

fmt:
  stage: generate
  variables:

    TF_ROOT_DIRS: aws
  extends: .fmt

checkov:
  extends: .checkov
  stage: test
  parallel:
    matrix:
      - TF_ROOT:
        - "ROOT1"
        - "ROOT2"

plan:
  extends: .plan
  parallel:
    matrix:
      - TF_ROOT:
        - "ROOT1"
        - "ROOT2"

infracost:
  stage: cost
  extends: .infracost
  variables:
    usage_file: ./infracost-usage.yml
  parallel:
    matrix:
      - TF_ROOT:
        - "ROOT1"
        - "ROOT2"

# Separate apply job for manual Terraform as it can be destructive action.
deploy:
  extends: .deploy
  parallel:
    matrix:
      - TF_ROOT:
        - "ROOT1"
        - "ROOT2"
```
