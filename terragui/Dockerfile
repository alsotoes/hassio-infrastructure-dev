# hadolint global ignore=DL3008
ARG BUILD_FROM
FROM ${BUILD_FROM}

ENV LANG C.UTF-8

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

# Copy application source
COPY app /app
COPY templates /app/templates
COPY static /app/static
COPY config/tfg.conf.example /app/config/tfg.conf.example
COPY run.py /app/run.py
COPY requirements.txt /app/requirements.txt
COPY pyproject.toml /app/pyproject.toml

# Install Python dependencies
RUN pip3 install --no-cache-dir -r /app/requirements.txt

# Install the package in development mode
RUN pip3 install --no-cache-dir -e /app

# Copy rootfs
COPY rootfs /

# Set entrypoint
ENTRYPOINT ["/init"]
CMD []