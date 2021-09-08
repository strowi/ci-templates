# Repo Testing

Can be disabled completely by setting the variable `REPO_SCANNING_DISABLED` to `true`.

## Dependency-Scanning with Trivy (stage: test)

Can be disabled by setting `DEPENDENCY_SCANNING_DISABLED` to `true`.

Very similar to [container-scanning](./tests_container.md), except easier.;)
This will test dependenvies with [aquasecurity/trivy](https://github.com/aquasecurity/trivy/) by using as most of gitlab's features as possible.

It will check for composer/yarn/caro/.. files and check those for CVEs.

### Usage

This will automatically run if you include the template and add a `stage` named `test`:


```yaml

include:
  ...
  - project: 'strowi/ci-templates'
    file: '/tests.yml'

stages:
  - ...
  - test
  - ...

```
This will clone + test the repository.

### Output

Will be in bash, not in MR-Widget, because we are not on the Ultimate Edition (yet).

```bash
2020-06-17T12:34:43.474+0200  DEBUG Severities: HIGH,CRITICAL
Enumerating objects: 2313, done.
Counting objects: 100% (2313/2313), done.
Compressing objects: 100% (1696/1696), done.
Total 2313 (delta 1204), reused 1400 (delta 552), pack-reused 0

Gemfile.lock
============
Total: 0 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 0, CRITICAL: 0)


ios/Gemfile.lock
================
Total: 1 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 1, CRITICAL: 0)

+---------------+------------------+----------+-------------------+------------------------+--------------------------------+
|    LIBRARY    | VULNERABILITY ID | SEVERITY | INSTALLED VERSION |     FIXED VERSION      |             TITLE              |
+---------------+------------------+----------+-------------------+------------------------+--------------------------------+
| activesupport | CVE-2020-8165    | HIGH     | 4.2.11.1          | ~> 5.2.4.3, >= 6.0.3.1 | rubygem-activesupport:         |
|               |                  |          |                   |                        | potentially unintended         |
|               |                  |          |                   |                        | unmarshalling of user-provided |
|               |                  |          |                   |                        | objects in MemCacheStore and   |
|               |                  |          |                   |                        | RedisCacheStore                |
+---------------+------------------+----------+-------------------+------------------------+--------------------------------+

```

## Credential-Scanning with gitleaks (stage: test)

Uses [Gitleaks](https://github.com/zricethezav/gitleaks) to scan the current branch/mr for secrets/credentials etc.

### Usage

This will automatically run if you include the template and add a `stage` named `test`:

```yaml
include:
  ...
  - project: 'strowi/ci-templates'
    file: '/tests.yml'

stages:
  - ...
  - test
  - ...
```

This will scan the current branch with trufflehog.

### Output

```
INFO[0000] opening .                                    
{
  "line": "# For a PostgreSQL database, use: \"postgresql://db_user:db_password@127.0.0.1:5432/db_name?serverVersion=11\u0026charset=utf8\"",
  "lineNumber": 26,
  "offender": "postgresql://db_user:db_password@127.0.0.1:5432/db_name?serverVersion=11\u0026charset=utf8\"",
  "commit": "4a2431e7924b1dc0b5506ec72c093875058c346d",
  "repo": ".",
  "repoURL": "",
  "leakURL": "",
  "rule": "Password in URL",
  "commitMessage": "Merge branch 'RI-204-redirect' into 'master'\n\nResolve RI-204 \"Redirect\"\n\nCloses RI-204\n\nSee merge request dev/video!2",
  "author": "Daniel Kolb",
  "email": "strowi@hasnoname.de",
  "file": ".env",
  "date": "2020-11-13T14:22:07Z",
  "tags": ""
}
{
  "line": "# For a PostgreSQL database, use: \"postgresql://db_user:db_password@127.0.0.1:5432/db_name?serverVersion=11\u0026charset=utf8\"",
  "lineNumber": 26,
...
```

### Custom rules

There are default rules, which can be extended by putting them into a `.gitleaks.toml`. Eg:

```
~>cat .gitleaks.toml
[allowlist]
description = "demo password"
regexes = ['''db_user:db_password''', '''root@mysql''']

```

For more examples see upstream documentation!


# Fixing things the right way!

You will need permissions to *force-push* to the repository in question.
So it's best to have 1 person fix all the repos for your team.
Then create a SYS-Issue asking for permissions for those repositories.

Since secrets are not gone just be overcommitting, you will need the [(B)ig(F)***ing(G)un Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/).
Just follow the original docs for installing + removing.
*Be careful* in removing important stuff that is still being needed (you know.. because of ...history.;)).
