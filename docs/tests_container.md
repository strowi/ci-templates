# Container Testing (stage: test)

This will test Container-Security with [aquasecurity/trivy](https://github.com/aquasecurity/trivy/) by using as most of gitlab's features as possible.

## Usage

Mostly: [Gitlab](https://gitlab.com/gitlab-org/security-products/analyzers/klar)

**Except** we added our own variables:

```bash
SUB_IMAGE_NAME - image path beneath the repo-path
TRIVY_USERNAME - Registry Username (default: `CI_REGISTRY_USER`)
TRIVY_PASSWORD - Registry Password (default: `CI_REGISTRY_PASSWORD`)
TRIVY_AUTH_URL - Registry URL      (default: `CI_REGISTRY`)
TRIVY_SEVERITY - Severity to show  (default: `HIGH,CRITICAL`)
```

```yaml
include:
  - project: 'sys/ci/templates'
    file: '/build.yml'
  - project: 'sys/ci/templates'
    file: '/tests.yml'

stages:
  - build
  - test

docker:build:
  extends: .build
  stage: build
  script:
    - build_image /test .

container_scanning:
  extends: .container_scanning
  variables:
    SUB_IMAGE_NAME: /test
```

If you want to *check multiple sub-image*:

```yaml
container_scanning:
  extends: .container_scanning
  parallel:
    matrix:
      - SUB_IMAGE_NAME:
        - /ttrss
        - /haproxy
```

This will build + test the image `$CI_REGISTRY_IMAGE/test:$CI_COMMIT_REF_SLUG`,
which should be fine for almost all use-cases.

If you want to test an external image you can override the following 2 variables:

```yaml
    CI_APPLICATION_REPOSITORY: "$CI_REGISTRY_IMAGE$SUB_IMAGE_NAME"
    CI_APPLICATION_TAG: "${CI_COMMIT_REF_SLUG}-${CI_COMMIT_SHORT_SHA}"
```

## Output

Will be in bash, not in MR-Widget, because we are not on the Ultimate Edition (yet).

```bash
[WARN] ▶ Image [registry.gitlab.com/strowi/xyz:proxy] contains 6 total vulnerabilities
[WARN] ▶ Image [registry.gitlab.com/strowi/xyz:proxy] contains 2 remediations
[ERRO] ▶ Image [registry.gitlab.com/strowi/xyz:proxy] contains 6 unapproved vulnerabilities
$ grep --color "[ERRO]" -A9999 -B9999 report.txt && exit 1 || exit 0
+------------+-----------------------+--------------+-------------------+--------------------------------------------------------------+
| STATUS     | CVE SEVERITY          | PACKAGE NAME | PACKAGE VERSION   | CVE DESCRIPTION                                              |
+------------+-----------------------+--------------+-------------------+--------------------------------------------------------------+
| Unapproved | Medium CVE-2019-5188  | e2fsprogs    | 1.44.1-1ubuntu1.2 | A code execution vulnerability exists in the                 |
|            |                       |              |                   | directory rehashing functionality of E2fsprogs               |
|            |                       |              |                   | e2fsck 1.45.4. A specially crafted ext4 directory            |
|            |                       |              |                   | can cause an out-of-bounds write on the stack,               |
|            |                       |              |                   | resulting in code execution. An attacker can                 |
|            |                       |              |                   | corrupt a partition to trigger this vulnerability.           |
|            |                       |              |                   | http://people.ubuntu.com/~ubuntu-security/cve/CVE-2019-5188  |
+------------+-----------------------+--------------+-------------------+------...
Uploading artifacts...
00:01
gl-container-scanning-report.json: found 1 matching files 
Uploading artifacts to coordinator... ok            id=53266 responseStatus=201 Created token=PoJCByAe
ERROR: Job failed: command terminated with exit code 1
```

## Ignoring Issues

Just add them to the `.trivyignore` WITH COMMENT!

```bash
$ cat .trivyignore
# Accept the risk
CVE-2018-14618

# No impact in our settings
CVE-2019-1543

```
