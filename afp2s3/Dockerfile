ARG BUILD_FROM
FROM arm64v8/ubuntu:jammy as buildlayer

RUN apt update -y && \
    apt upgrade -y && \
    apt install -y build-essential libgcrypt-dev \
                   libfuse-dev libgmp-dev libreadline-dev \
                   libncurses-dev wget git && \
    git clone https://github.com/alsotoes/afpfs-ng.git && \
    wget https://gist.githubusercontent.com/alsotoes/d64cb088cbbc210fdafba090fab0ebd4/raw/1c1800ef50402095fbde78d2e45e4170a63be1bd/afpfs-ng_headup-patch00.diff && \
    patch -p0 < afpfs-ng_headup-patch00.diff && cd afpfs-ng && \
    CFLAGS="-I/usr/local/include -L/usr/local/lib" ./configure --build=arm-unknown-linux-gnu && make && make install

FROM ${BUILD_FROM}

RUN apk add --no-cache \
    build-base libgcrypt-dev ca-certificates \
    fuse-dev gmp-dev glib util-linux jq \
    readline-dev ncurses-dev libgpg-error openssl \
    wget iproute2 fuse fuse3 libgcrypt libc6-compat musl \
    && rm -rf /var/cache/apk /tmp /sbin/halt /sbin/poweroff /sbin/reboot \
    && mkdir -p /usr/local/{bin,lib}

COPY --from=buildlayer /usr/local/bin/afpfs /usr/local/bin/afpfs
COPY --from=buildlayer /usr/local/bin/afpfsd /usr/local/bin/afpfsd
COPY --from=buildlayer /usr/local/bin/afpcmd /usr/local/bin/afpcmd
COPY --from=buildlayer /usr/local/bin/mount_afp /usr/local/bin/mount_afp
COPY --from=buildlayer /usr/local/bin/afp_client /usr/local/bin/afp_client
COPY --from=buildlayer /usr/local/bin/afpgetstatus /usr/local/bin/afpgetstatus
COPY --from=buildlayer /usr/local/lib/libafpclient.a /usr/local/lib/libafpclient.a
COPY --from=buildlayer /usr/local/lib/libafpclient.la /usr/local/lib/libafpclient.la
COPY --from=buildlayer /usr/local/lib/libafpclient.so /usr/local/lib/libafpclient.so
COPY --from=buildlayer /usr/local/lib/libafpclient.so.0 /usr/local/lib/libafpclient.so.0
COPY --from=buildlayer /usr/local/lib/libafpclient.so.0.0.0 /usr/local/lib/libafpclient.so.0.0.0

# Copy data for add-on
COPY rootfs /
RUN chmod a+x /etc/cont-init.d/10-afp-mount /etc/services.d/minio/run

CMD [ "/init" ]
