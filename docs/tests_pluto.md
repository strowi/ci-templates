# Manifests deprecation/removal checks

ref: <https://github.com/FairwindsOps/pluto>

Can be disabled completely by setting the variable
`MANIFESTS_SCANNING_DISABLED` to `true`.

This will check the specified folders for k8s-deprecations / removals. E.g.:

```yaml
NAME      KIND      VERSION                    REPLACEMENT            REMOVED   DEPRECATED  
xyz      Ingress   networking.k8s.io/v1beta1   networking.k8s.io/v1   false     true        

```

For now the k8s-version will be specified as variable.
Fater we can switch to checking against the running cluster.

```yaml
  variables:
    K8S_VERSION: v1.20.5
    KUBE_MANIFESTS_DIR: ./manifests
```

By default this will scan following folders:

* all folders containing a `chart.yaml` file (Helm-Chart)
* `./manifests`-folder (sys)
* `./config`-folder (dev)
