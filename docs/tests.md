# Test templates

Some templates for testing docker-images, services. etc...
By including this, some jobs might run automagically, like:

* [codequality](./tests_codequality.md)
  * [Code](../tests/Jobs/Code-Quality.gitlab-ci.yml)
* [container_scanning](./tests_container.md)
  * [Code](../tests/Security/Container-Scanning.gitlab-ci.yml)
* [dependency_scanning](./tests_repo.md)
  * [Code](../tests/Security/Dependency-Scanning.gitlab-ci.yml)
* [sast_scanning](./tests_repo.md)
  * [Code](../tests/Security/SAST.gitlab-ci.yml)
* [secret_detection](./tests_repo.md)
  * [Code](../tests/Security/Secret-Detection.gitlab-ci.yml)
* [dgoss](./dgoss.md)
  * [Code](../tests/dgoss.yml)
* [GPG](./tests_gpg-keys.md)
  * [Code](../tests/Jobs/GPG-Keys.yml)
