ARG BUILD_FROM=ghcr.io/hassio-addons/base-nodejs:0.2.1
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Copy Node-RED package.json
COPY package.json /opt/
COPY node-red-dashboard-show-dashboard.patch /tmp/

# Set workdir
WORKDIR /opt

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
#RUN apk add --no-cache --virtual .build-dependencies build-base linux-headers py3-pip python3-dev

# Install build dependencies individually for clarity
RUN apk add --no-cache --virtual .build-dependencies build-base
RUN apk add --no-cache --virtual .build-dependencies linux-headers
RUN apk add --no-cache --virtual .build-dependencies py3-pip
RUN apk add --no-cache --virtual .build-dependencies python3-dev

#RUN apk update
RUN apk add --no-cache git
RUN apk add --no-cache icu-data-full=74.1-r0
RUN apk add --no-cache nginx=1.24.0-r16
#RUN apk add --no-cache openssh-client=9.6_p1-r0
RUN apk add --no-cache openssh-client
RUN apk add --no-cache patch=2.7.6-r10

RUN npm install --no-audit --no-fund --no-update-notifier --omit=dev --unsafe-perm
RUN npm rebuild --build-from-source @serialport/bindings-cpp
RUN npm cache clear --force
RUN echo -e "StrictHostKeyChecking no" >> /etc/ssh/ssh_config
RUN patch -d /opt/node_modules/node-red-dashboard -p1 -i /tmp/node-red-dashboard-show-dashboard.patch
RUN apk del --no-cache --purge .build-dependencies
RUN rm -fr /etc/nginx /root/.cache /root/.npm /root/.nrpmrc /tmp/*


# Copy root filesystem
COPY rootfs /

# Health check
HEALTHCHECK --start-period=10m \
    CMD curl --fail http://127.0.0.1:46837 || exit 1

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
