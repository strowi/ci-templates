# build

This one serves as example for the build-related includes.

```yaml
include:
  - project: 'strowi/ci-templates'
    file: '/build.yml'

build:docker:default:
  extends: .build
  variables:
    PLATFORMS: linux/amd64
  script:
    - build_image
...
```

For docker-registry maintenance to work without problems, and your
production images not to get deleted it is at the very least
required that you tag your images in the following way:

`${CI_COMMIT_REF_SLUG}-${CI_COMMIT_SHORT_SHA}`

These templates will do this.

## Usage

Includes the template from[/strowi/ci-templates/blob/master/build.yml](https://gitlab.com/strowi/ci-templates/blob/master/build.yml).
Besides setting up docker (with registry-login, buildx) it serves
some common build-related functions:

```bash
# enable docker-buildkit
# docker-login
- docker_setup

# docker_setup
# build ./Dockerfile -> "${CI_REGISTRY_IMAGE}:${CI_COMMIT_REF_SLUG}-${CI_COMMIT_SHA}"
# analyze_image
- build_image

# docker_setup
# builds ./sub_dir/Dockerfile -> "${CI_REGISTRY_IMAGE}/sub_name:${CI_COMMIT_REF_SLUG}-${CI_COMMIT_SHA}"
- build_image /sub_name sub_dir

# docker_setup
# release image "${CI_REGISTRY_IMAGE}:${CI_COMMIT_REF_SLUG}-${CI_COMMIT_SHA}" -> "${CI_REGISTRY_IMAGE}:latest"
- release_latest

# docker_setup
# releases image "${CI_REGISTRY_IMAGE}/${sub_dir}:${CI_COMMIT_REF_SLUG}-${CI_COMMIT_SHA}" -> "${CI_REGISTRY_IMAGE}/${sub_dir}:latest"
- release_latest /$sub_name

# docker_setup
# docker pull "${CI_REGISTRY_IMAGE}/${sub_dir}:${CI_COMMIT_REF_SLUG}-${CI_COMMIT_SHA}"
# docker tag "${CI_REGISTRY_IMAGE}/${sub_dir}:${CI_COMMIT_REF_SLUG}-${CI_COMMIT_SHA}" -> :latest
- depend_image /$sub_name
```

## Multi-stage builds

If you want to build multiple images that depend *directly* on the
other, you can just call build $x multiple times in 1 stage,
or utilize separate stages.

php-Dockerfile:
```
FROM registry.gitlab.com/x/y/z/base:latest
...
```

```yaml
include:
  - project: 'strowi/ci-templates'
    file: '/build.yml'

docker:build:
  extends: .build
  stage: build
  script:
    - build_image /base ./base_dir
    - build_image /php ./php_dir
```

This will build, tag + push:

- $CI_REGISTRY_IMAGE/base:${CI_COMMIT_REG_SLUG}-${CI_COMMIT_SHORT_SHA}
- $CI_REGISTRY_IMAGE/base:${CI_COMMIT_REG_SLUG}

as normal and the depending images:

- $CI_REGISTRY_IMAGE/php:${CI_COMMIT_REG_SLUG}-${CI_COMMIT_SHORT_SHA}
- $CI_REGISTRY_IMAGE/php:${CI_COMMIT_REG_SLUG}

Both will be pushed too.

### Running Commands inbetween

The above works fine for images directly depending on the other. But what if you need to run e.g. composer in-between and want to take advantage of gitlabs-caching?

You can do sth. like the following, which will:
* build + push php-cli
* run composer with the above php-cli image on the code + save artifacts
* download artifacts + build + push app 

```yaml

stages:
  - build1
  - composer
  - build2

docker:build:php-cli:
  extends: .build
  stage: build1
  script:
    - build_image /php-cli components/php-cli

composer:
  extends: .composer
  image: ${CI_REGISTRY_IMAGE}/php-cli:${CI_COMMIT_REF_SLUG}-${CI_COMMIT_SHORT_SHA}
  script:
    - composer install -d components/app

  artifacts:
    expire_in: 30 min
    paths:
    - components/app/vendor/

docker:build:app:
  extends: .build
  stage: build2
  dependencies:
    - composer
  script:
    - depend_image /php-cli
    - build_image /app components/app

```
