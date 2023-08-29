# Codequality Check

## CodeClimate

This test runs during the "test"-stage, and checks the
"codequality" by help of [codeclimate](https://docs.codeclimate.com/docs/list-of-engines).

On Merge-Requests it will show the results in a widget.

These can be further configured (see above link).
The template should be identical to the gitlab-provided one,
except a tag `codequality` to run this on a specific runner.

This way we can keep the downloaded images on the runner,
and don't have to re-download > 2GB docker-images every
time (like with the k8s-dind-runners).

If you really don't want to run these tests, but include
some of the templates, you can disable these by setting:

```yaml
variables:
  CODE_QUALITY_DISABLED: "true"
```

## Yamllint

There is also a default Yaml-Linter which can be disabled by variable: `YAMLLINT_DISABLED`.
Configuration can be done. See <https://github.com/adrienverge/yamllint>.
