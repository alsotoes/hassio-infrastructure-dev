ARG BUILD_FROM
# Stage 1: Build Go binary and clone repo
FROM golang:1.22-alpine AS builder

RUN apk add --no-cache git build-base sqlite-dev \
    && git clone https://github.com/karam-ajaj/atlas.git /atlas-repo
WORKDIR /atlas-repo
RUN git checkout 3.1.11

# Build the Go binary
WORKDIR /atlas-repo/config/atlas_go
RUN go mod init atlas || true
RUN go build -o /atlas .

# Stage 2: Runtime
FROM ${BUILD_FROM}

RUN apk add --no-cache \
    nginx iputils-ping traceroute nmap sqlite net-tools curl jq ca-certificates git py3-pip && \
    python3 -m venv /opt/venv

# Create a virtual environment

# Install python packages
ENV PATH="/opt/venv/bin:$PATH"
# hadolint ignore=SC1091
RUN source /opt/venv/bin/activate && pip install --no-cache-dir fastapi uvicorn

# Remove default Nginx config
RUN rm -f /etc/nginx/conf.d/default.conf /etc/nginx/sites-enabled/default || true

ENV ATLAS_HOME=/opt/atlas

# Copy from builder stage
RUN mkdir -p ${ATLAS_HOME}
COPY --from=builder /atlas-repo/data/html/ /usr/share/nginx/html/
COPY --from=builder /atlas /usr/local/bin/atlas
COPY --from=builder /atlas-repo ${ATLAS_HOME}

COPY rootfs /
# Make all shell scripts executable
RUN if [ -d /config/scripts ]; then \
      find /config/scripts -type f -name '*.sh' -exec chmod +x {} +; \
    fi && chmod +x /run.sh

# Set default ports (can be overridden at runtime)
ENV ATLAS_UI_PORT=8888
ENV ATLAS_API_PORT=8889

# Entrypoint: initializes DB, runs scans, launches FastAPI and Nginx
EXPOSE 8888/tcp 
EXPOSE 8889/tcp
CMD ["/run.sh"]
