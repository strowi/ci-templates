## goss / dgoss

Quick and Easy server testing/validation [https://goss.rocks](https://goss.rocks)

### Usage

This template provides some reasonable defaults for running dgoss,
you can override them if you need to.

```yaml
include:
  - project: 'strowi/ci-templates'
    file: '/tests.yml'

dgoss:test:
  extends: .dgoss
  stage: test
  variables:
    GOSS_SLEEP: 10s
  script:
    - docker_setup
    - dgoss run $OPTIONS > goss.xml
```

This will do docker_setup and run dgoss with the following options:

```bash
GOSS_OPTS = --format junit
GOSS_FILES_STRATEGY = cp
```

Then you can do `dgoss run ...` in your script. For JUnit-compatible output and display in MRs pipe this to `./goss.xml` it should be automatically picked up as MR-Artifact.

**Important** The `--format junit` will output a goss.xml, which will automatically be picked up as artifact and displayed in Merge-Requests.

```bash
$ dgoss_setup
...
# setting up dgoss:
  GOSS_OPTS = --format junit
  GOSS_FILES_STRATEGY = cp

$ dgoss run "${CI_REGISTRY_IMAGE}/${CI_COMMIT_REF_SLUG}:${CI_COMMIT_SHA}" > goss.xml
INFO: Creating docker container
Unable to find image 'registry.gitlab.com/strowi/test-output:9133610453c1c87986af285e277671f1cbd94c76' locally
9133610453c1c87986af285e277671f1cbd94c76: Pulling from strowi/test-output
000eee12ec04: Pulling fs layer
eb22865337de: Pulling fs layer
bee5d581ef8b: Pulling fs layer
bee5d581ef8b: Download complete
eb22865337de: Verifying Checksum
eb22865337de: Download complete
000eee12ec04: Download complete
000eee12ec04: Pull complete
eb22865337de: Pull complete
bee5d581ef8b: Pull complete
Digest: sha256:d807b1599422f4f927e5bbc735fb779a51263d01ffd227afc9a9b80c0e4caaca
Status: Downloaded newer image for registry.gitlab.com/strowi/test-output:9133610453c1c87986af285e277671f1cbd94c76
INFO: Copy goss files into container
INFO: Starting docker container
INFO: Container ID: 029d844c
INFO: Sleeping for 0.2
INFO: Container health
INFO: Running Tests
INFO: Deleting container
Uploading artifacts...
goss.xml: found 1 matching files
Uploading artifacts to coordinator... ok            id=49695 responseStatus=201 Created token=SZbdRNBo
...
```
