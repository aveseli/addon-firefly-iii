ARG BUILD_FROM=ghcr.io/hassio-addons/base/amd64:11.1.0
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]


# modified version from php:alpine image

# ensure www-data user exists
RUN set -x ; \
  addgroup -g 82 -S www-data ; \
  adduser -u 82 -D -S -G www-data www-data && exit 0 ; exit 1

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
    php8-pdo_mysql \
    php8-mysqlnd \
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

RUN FIREFLY_VERSION=5.7.0 \
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