FROM rancher/curl AS fetcher

RUN apk add curl

# renovate: datasource=github-releases depName=docker/buildx versioning=loose
ENV BUILDX_VERSION=0.4.2

RUN curl -L \
  --output /docker-buildx \
  "https://github.com/docker/buildx/releases/download/v${BUILDX_VERSION}/buildx-v${BUILDX_VERSION}.linux-amd64" \
  && chmod a+x /docker-buildx

RUN curl -L \
  https://github.com/google/go-containerregistry/releases/download/v0.8.0/go-containerregistry_Linux_x86_64.tar.gz \
  && tar xvf go-containerregistry_Linux_x86_64.tar.gz \
  && chmod a+x /crane

FROM docker:20.10

COPY --from=fetcher /docker-buildx /usr/lib/docker/cli-plugins/docker-buildx
COPY --from=fetcher /crane /usr/bin/crane
