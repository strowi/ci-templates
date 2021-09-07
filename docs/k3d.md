# k3d / kubernetes-testing

This will run a kubernetes-cluster with [k3d/k3s](https://github.com/rancher/k3d).
By default utilizes [deploy-image](https://gitlab.com/strowi/deploy),
but you should be able to use *any* docker-image to run this.

Only requirement for the image is `wget`. Detection for alpine/ubuntu
should work OOB.

Requirements:

* kubernete/docker-runner (our default).
* `wget` within the used image.
  Detection for alpine/ubuntu should work OOB and install if not found.

If not, contact sales...err maintainer.;P

```yaml
include:
  - project: 'strowi/ci-templates'
    file: '/k3d.yml'

stages:
  - test

# by default uses our deploy-image containing deploy-k8
k3d:test:deploy-k8s:
  stage: test
  extends: .k3d
  variables:
    K3D_VERSION: rancher/k3s:v1.18.8-k3s1 # for other than latest stable
  script:
    - start_k3d
    - deploy-k8
    - get_pod nginx

# using an ubuntu-base image
k3d:test:ubuntu:
  stage: test
  extends: .k3d
  image: ubuntu:20.04
  script:
    - start_k3d
    - kubectl create ns $KUBE_NAMESPACE
    - kubectl -n $KUBE_NAMESPACE apply -f manifests
    - get_pod nginx

# using an alpine base-image
k3d:test:alpine:
  stage: test
  extends: .k3d
  image: alpine:latest
  script:
    - start_k3d
    - kubectl create ns $KUBE_NAMESPACE
    - kubectl -n $KUBE_NAMESPACE apply -f manifests
    - get_pod nginx
```

Thats it, afterwards you can almost do whatever you want.;)
