# hadolint global ignore=DL3008
ARG BUILD_FROM
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

ENV LANG=C.UTF-8

# Install Python 3, pip, and terraform
RUN apk add --no-cache \
    python3 \
    py3-pip \
    terraform \
    git \
    bash \
    ca-certificates

# Create app directory
WORKDIR /app

# Copy application source (preserving package structure)
COPY app /app/app
COPY templates /app/templates
COPY static /app/static
COPY config/tfg.conf.example /app/app/config/tfg.conf.example
COPY run.py /app/run.py
COPY requirements.txt /app/requirements.txt
COPY pyproject.toml /app/pyproject.toml

# Install Python dependencies
RUN pip3 install --no-cache-dir -r /app/requirements.txt

# Install the package in development mode
RUN pip3 install --no-cache-dir -e /app/app

# Copy rootfs
COPY rootfs /

# Set execute permissions on scripts
RUN chmod +x /etc/cont-init.d/terragui && \
    chmod +x /etc/services.d/terragui/run && \
    chmod +x /etc/services.d/terragui/finish && \
    chmod +x /usr/local/bin/health-check

# Set entrypoint
ENTRYPOINT ["/init"]
CMD []

# Build arguments
ARG BUILD_VERSION
ARG BUILD_ARCH

# Labels
LABEL \
    io.hass.name="TerraGUI Terraform Manager" \
    io.hass.description="Local Terraform Cloud-like UI for managing Terraform workspaces" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Alvaro Soto <alsotoes@gmail.com>" \
    org.opencontainers.image.title="terragui" \
    org.opencontainers.image.description="Local Terraform Cloud-like UI for managing Terraform workspaces" \
    org.opencontainers.image.vendor="Alvaro Soto" \
    org.opencontainers.image.authors="Alvaro Soto <alsotoes@gmail.com>" \
    org.opencontainers.image.licenses="MIT license" \
    org.opencontainers.image.url="https://headup.ws" \
    org.opencontainers.image.source="https://github.com/alsotoes/hassio-infrastructure-dev" \
    org.opencontainers.image.version=${BUILD_VERSION}