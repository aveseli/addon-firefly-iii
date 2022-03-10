ARG BUILD_FROM=homeassistant/amd64-base:latest
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]


# modified version from php:alpine image

# ensure www-data user exists
RUN set -x ; \
  addgroup -g 82 -S www-data ; \
  adduser -u 82 -D -S -G www-data www-data && exit 0 ; exit 1
# 82 is the standard uid/gid for "www-data" in Alpine
# http://git.alpinelinux.org/cgit/aports/tree/main/apache2/apache2.pre-install?h=v3.3.2
# http://git.alpinelinux.org/cgit/aports/tree/main/lighttpd/lighttpd.pre-install?h=v3.3.2
# http://git.alpinelinux.org/cgit/aports/tree/main/nginx-initscripts/nginx-initscripts.pre-install?h=v3.3.2

RUN apk update && apk upgrade

RUN apk add --no-cache bash curl unzip mariadb-client

RUN apk add --no-cache \
    php8 \
    php8-curl \
    php8-iconv \
    php8-json \
    php8-mbstring \
    php8-openssl \
    php8-phar \
    php8-zip \
    php8-bcmath \
    php8-cli \
    php8-common \
    php8-fpm \
    php8-gd \
    php8-intl \
    php8-ldap \
    php8-mysqli \
    php8-opcache \
    php8-sqlite3 \
    php8-xml \
    php8-simplexml \
    php8-xmlwriter \
    php8-tokenizer \
    php8-sodium \
    php8-session \
    php8-pdo \
    php8-fileinfo \
    php8-dom

RUN ln -s /usr/bin/php8 /usr/bin/php

# RUN curl -s https://getcomposer.org/installer | php8 && alias composer='php8 composer.phar'
RUN curl -sS https://getcomposer.org/installer | php8 -- --install-dir=/usr/local/bin --filename=composer

RUN mkdir -p /var/www

RUN FIREFLY_VERSION=5.6.16 \
        \
    && curl -J -L -o /tmp/firefly.tar.gz \
        https://github.com/firefly-iii/firefly-iii/archive/${FIREFLY_VERSION}.tar.gz \
    &&  mkdir -p /var/www/firefly \
    && tar zxf /tmp/firefly.tar.gz -C \
        /var/www/firefly --strip-components=1 \
    && cd /var/www/firefly \
    && composer install --prefer-dist --no-dev --no-scripts --no-suggest

RUN apk add nginx
RUN rm -f -r \
        /tmp/* \
        /etc/nginx \
        /var/{cache,log}/* \
        /var/www/firefly/Docker* \
        /var/www/firefly/docker* \
        /var/www/firefly/phpunit* \
        \
    && mkdir -p /var/log/nginx \
    && touch /var/log/nginx/error.log

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="Firefly III" \
    io.hass.description="A free and open source personal finance manager" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version="master" \
    maintainer="Paul Sinclair <hello@addons.community>" \
    org.opencontainers.image.title="Firefly III" \
    org.opencontainers.image.description="A free and open source personal finance manager" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Paul Sinclair <hello@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/hassio-addons/addon-firefly-iii" \
    org.opencontainers.image.documentation="https://github.com/hassio-addons/addon-firefly-iii/blob/master/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
