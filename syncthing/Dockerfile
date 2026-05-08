ARG SYNCTHING_VERSION=v2.0.16

FROM golang:1.26-alpine AS builder

RUN apk --no-cache add ca-certificates git
WORKDIR /build

ARG SYNCTHING_VERSION
RUN git clone --tags https://github.com/syncthing/syncthing
RUN cd syncthing && git checkout "${SYNCTHING_VERSION}"
RUN cd syncthing && go run build.go
RUN cd syncthing && cp bin/syncthing /tmp/syncthing

FROM gcr.io/distroless/static-debian13:nonroot AS default-image

COPY --from=builder /tmp/syncthing /syncthing

ENTRYPOINT ["/syncthing"]
CMD ["serve"]
