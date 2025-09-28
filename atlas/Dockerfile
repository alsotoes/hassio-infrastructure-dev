ARG BUILD_FROM
# Stage 1: Build Go binary and clone repo
FROM golang:1.22-alpine AS builder

WORKDIR /app
RUN apk add --no-cache git build-base sqlite-dev \
    && git clone https://github.com/karam-ajaj/atlas.git /app \
    && git checkout 3.1.11

WORKDIR /app/config/atlas_go
ENV CGO_CFLAGS="-D_LARGEFILE64_SOURCE"
RUN if [ ! -f go.mod ]; then go mod init atlas || true; fi \
    && go mod init atlas || true \
    && go build -o /atlas .

# Stage 2: Runtime
FROM ${BUILD_FROM}

RUN apk add --no-cache \
    nginx iputils-ping traceroute nmap sqlite net-tools curl jq ca-certificates git py3-pip && \
    python3 -m venv /opt/venv

# Install python packages
ENV PATH="/opt/venv/bin:$PATH"
# hadolint ignore=SC1091
RUN source /opt/venv/bin/activate \ 
    && pip install --no-cache-dir fastapi uvicorn \
    && rm -f /etc/nginx/conf.d/default.conf /etc/nginx/sites-enabled/default || true

# Copy from builder stage
RUN mkdir -p /config
COPY --from=builder /app/data/html/ /usr/share/nginx/html/
COPY --from=builder /atlas /config/bin/atlas
COPY --from=builder /app/config/scripts /config/scripts
COPY --from=builder /app/config/nginx /config/nginx

# Make all shell scripts executable
RUN if [ -d /config/scripts ]; then \
      find /config/scripts -type f -name '*.sh' -exec chmod +x {} +; \
    fi

# Set default ports (can be overridden at runtime)
ENV ATLAS_UI_PORT=8888
ENV ATLAS_API_PORT=8889
# Entrypoint: initializes DB, runs scans, launches FastAPI and Nginx
CMD ["/config/scripts/atlas_check.sh"]
