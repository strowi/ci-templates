FROM alpine AS fetcher

RUN apk add curl tar

# renovate: datasource=github-releases depName=google/go-containerregistry versioning=loose
ENV CRANE_VERSION=0.20.0
RUN curl -L \
  --output /go-containerregistry_Linux_x86_64.tar.gz \
  https://github.com/google/go-containerregistry/releases/download/v${CRANE_VERSION}/go-containerregistry_Linux_x86_64.tar.gz \
  && tar xvf go-containerregistry_Linux_x86_64.tar.gz \
  && chmod a+x /crane

FROM docker:27.0

COPY --from=fetcher /crane /bin/crane
