FROM php:7.4-fpm-alpine as php

RUN apk update && apk add --no-cache \
        zip \
        libzip-dev \
        libpng \
        freetype-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        libjpeg \
        icu \
        icu-dev \
        libxml2 \
        libxml2-dev \
        openssl \
        openssl-dev \
        oniguruma-dev \
        postgresql-client \
        postgresql-dev \
        git

FROM php as php-xdebug

RUN set -xe \
    && apk add --no-cache --virtual .phpize-deps $PHPIZE_DEPS  \
    && pecl install xdebug redis \
    && docker-php-ext-enable xdebug redis \
    && apk del --no-network .phpize-deps

RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install \
    exif \
    pdo \
    pdo_pgsql \
    -j$(nproc) gd \
    mbstring \
    intl \
    xml \
    sockets \
    opcache \
    zip \
    && echo "xdebug.default_enable=off" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_autostart=on" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_port=9001" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_handler=dbgp" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_connect_back=0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.idekey=phpstorm" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_host=10.254.254.254" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && rm -rf /tmp/*

COPY --from=composer /usr/bin/composer /usr/bin/composer
COPY php.ini /usr/local/etc/php
