FROM alpine:latest as source

RUN apk add curl && \
    curl -LO "https://github.com/siderolabs/omni/releases/download/v0.31.1/omni-linux-arm64" && \
    curl -LO "https://github.com/siderolabs/omni/releases/download/v0.31.1/omnictl-linux-arm64" && \
    chmod +x /omni-linux-arm64 && \
    chmod +x /omnictl-linux-arm64

FROM ghcr.io/siderolabs/omni:latest

COPY --from=source /omni-linux-arm64 /omni
COPY --from=source /omnictl-linux-arm64 /omnictl
