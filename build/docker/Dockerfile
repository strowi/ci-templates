FROM alpine AS fetcher

RUN apk add curl tar

# renovate: datasource=github-releases depName=docker/buildx versioning=loose
ENV BUILDX_VERSION=0.4.2
RUN curl -L \
  --output /docker-buildx \
  "https://github.com/docker/buildx/releases/download/v${BUILDX_VERSION}/buildx-v${BUILDX_VERSION}.linux-amd64" \
  && chmod a+x /docker-buildx

# renovate: datasource=github-releases depName=google/go-containerregistry versioning=loose
ENV CRANE_VERSION=0.8.0
RUN curl -L \
  --output /go-containerregistry_Linux_x86_64.tar.gz \
  https://github.com/google/go-containerregistry/releases/download/v${CRANE_VERSION}/go-containerregistry_Linux_x86_64.tar.gz \
  && tar xvf go-containerregistry_Linux_x86_64.tar.gz \
  && chmod a+x /crane

FROM docker:23.0

COPY --from=fetcher /docker-buildx /usr/lib/docker/cli-plugins/docker-buildx
COPY --from=fetcher /crane /bin/crane
