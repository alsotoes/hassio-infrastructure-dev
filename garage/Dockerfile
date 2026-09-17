ARG BUILD_FROM
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

ARG BUILD_ARCH
ARG GARAGE_VERSION="v2.4.1"
ARG S6_OVERLAY_VERSION="3.1.2.1"
ENV GARAGE_VERSION=${GARAGE_VERSION} \
    ARCH=${BUILD_ARCH}

# Use bash for the build script
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN \
    if [ "${ARCH}" = "aarch64" ]; then export GARAGE_ARCH="aarch64-unknown-linux-musl"; export S6_ARCH="aarch64"; fi && \
    if [ "${ARCH}" = "amd64" ]; then export GARAGE_ARCH="x86_64-unknown-linux-musl"; export S6_ARCH="x86_64"; fi && \
    if [ "${ARCH}" = "armv7" ]; then export GARAGE_ARCH="armv7-unknown-linux-musleabihf"; export S6_ARCH="arm"; fi && \
    if [ "${ARCH}" = "armhf" ]; then export GARAGE_ARCH="armv6-unknown-linux-musleabihf"; export S6_ARCH="arm"; fi && \
    mkdir -p /tmp/garage-download && \
    curl -L -s "https://garagehq.deuxfleurs.fr/_releases/${GARAGE_VERSION}/${GARAGE_ARCH}/garage" -o /usr/local/bin/garage && \
    chmod +x /usr/local/bin/garage && \
    curl -L -s "https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-noarch.tar.xz" | tar -C / -Jxpf - && \
    curl -L -s "https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-${S6_ARCH}.tar.xz" | tar -C / -Jxpf - && \
    rm -rf /tmp/*

COPY rootfs /

RUN chmod +x /etc/cont-init.d/garage && \
    chmod +x /etc/services.d/garage/run && \
    chmod +x /etc/services.d/garage/finish && \
    chmod +x /usr/local/bin/health-check

WORKDIR /
EXPOSE 3900/tcp 3901/tcp 3902/tcp 3903/tcp
HEALTHCHECK \
    --interval=30s \
    --timeout=5s \
    --start-period=10s \
    CMD /usr/local/bin/health-check || exit 1

ENTRYPOINT ["/init"]

# Build arguments
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="Garage S3 Storage" \
    io.hass.description="S3-compatible distributed object storage for Home Assistant" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Alvaro Soto <alsotoes@gmail.com>" \
    org.opencontainers.image.title="garage" \
    org.opencontainers.image.description="S3-compatible distributed object storage for Home Assistant" \
    org.opencontainers.image.vendor="Alvaro Soto" \
    org.opencontainers.image.authors="Alvaro Soto <alsotoes@gmail.com>" \
    org.opencontainers.image.licenses="GPL-3.0 license" \
    org.opencontainers.image.url="https://headup.ws" \
    org.opencontainers.image.source="https://github.com/alsotoes/hassio-infrastructure-dev" \
    org.opencontainers.image.version=${BUILD_VERSION}