ARG CADDY_VERSION=2.10.0
ARG GO_VERSION=1.25
FROM --platform=$BUILDPLATFORM golang:${GO_VERSION}-alpine AS builder
ARG CADDY_VERSION
ARG TARGETOS
ARG TARGETARCH

RUN apk add --no-cache git
RUN go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest
RUN GOOS=${TARGETOS} GOARCH=${TARGETARCH} CGO_ENABLED=0 /go/bin/xcaddy build "v${CADDY_VERSION}" \
    --with github.com/lucaslorentz/caddy-docker-proxy/plugin/v2 \
    --with github.com/caddy-dns/bunny \
    --with github.com/caddy-dns/cloudflare \
    --output /usr/bin/caddy

FROM caddy:${CADDY_VERSION}-alpine
LABEL org.opencontainers.image.source="https://github.com/fangmarks/tools"
ENV CADDY_DOCKER_APIS_VERSION=1.44

COPY --from=builder /usr/bin/caddy /usr/bin/caddy

CMD ["caddy", "docker-proxy"]
