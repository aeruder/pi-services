ARG SYNCTHING_VERSION=v2.0.16
ARG BUILDPLATFORM
FROM --platform=$BUILDPLATFORM golang:1.26-alpine AS builder

RUN uname -a
RUN apk --no-cache add ca-certificates git
WORKDIR /build

ARG SYNCTHING_VERSION
RUN git clone --depth 1 --revision refs/tags/"${SYNCTHING_VERSION}" https://github.com/syncthing/syncthing

WORKDIR /build/syncthing

RUN go mod download
RUN go generate github.com/syncthing/syncthing/lib/api/auto \
    github.com/syncthing/syncthing/cmd/infra/strelaypoolsrv/auto

ARG TARGETOS
ARG TARGETARCH
RUN set -x ; \
    env GOOS="${TARGETOS}" GOARCH="${TARGETARCH}" \
   go install -ldflags "-w \
    -buildid= \
 -X github.com/syncthing/syncthing/lib/build.Version=${SYNCTHING_VERSION} \
 -X github.com/syncthing/syncthing/lib/build.Stamp=$(date +%s) \
 -X github.com/syncthing/syncthing/lib/build.User=docker \
 -X github.com/syncthing/syncthing/lib/build.Host=docker \
 -X github.com/syncthing/syncthing/lib/build.Tags=" \
    github.com/syncthing/syncthing/cmd/stdiscosrv \
    github.com/syncthing/syncthing/cmd/strelaysrv \
    github.com/syncthing/syncthing/cmd/syncthing
RUN cp -v "$(find "$GOPATH" -name syncthing -type f -print -quit)" /tmp/syncthing

FROM gcr.io/distroless/static-debian13:nonroot AS default-image

COPY --from=builder /tmp/syncthing /syncthing

ENTRYPOINT ["/syncthing"]
CMD ["serve"]
