# ---------- Builder ----------
FROM golang:1.24-alpine AS builder

WORKDIR /build

RUN apk add --no-cache git

COPY . .

RUN go mod download

RUN CGO_ENABLED=0 GOOS=linux GOARCH=arm64 \
    go build \
    -ldflags="-s -w" \
    -o /out/noah-mqtt \
    ./cmd/noah-mqtt

# ---------- Home Assistant ----------
FROM ghcr.io/home-assistant/base:latest

LABEL io.hass.type="app"

COPY config.json /

RUN apk add --no-cache jq curl

COPY --from=builder /out/noah-mqtt /noah-mqtt

COPY rootfs/docker-entrypoint.sh /

RUN chmod +x /noah-mqtt \
 && chmod +x /docker-entrypoint.sh

ENTRYPOINT ["/docker-entrypoint.sh"]
