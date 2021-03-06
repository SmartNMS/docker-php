#
# docker build -t smartnms/php:7.1.6-fpm-alpine .
#
FROM php:7.1.6-fpm-alpine

LABEL maintainer="wingsun97@qq.com"

# docker-entrypoint.sh dependencies
RUN apk add --no-cache \
# in theory, docker-entrypoint.sh is POSIX-compliant, but priority is a working, consistent image
                bash \
# BusyBox sed is not sufficient for some of our sed expressions
                sed

# install the PHP extensions we need
RUN set -ex; \
        \
        apk add --no-cache --virtual .build-deps \
                libjpeg-turbo-dev \
                libpng-dev \
        ; \
        \
        docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr; \
        docker-php-ext-install gd mysqli opcache zip; \
        \
        runDeps="$( \
                scanelf --needed --nobanner --recursive \
                        /usr/local/lib/php/extensions \
                        | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
                        | sort -u \
                        | xargs -r apk info --installed \
                        | sort -u \
        )"; \
        apk add --virtual .wordpress-phpexts-rundeps $runDeps; \
        apk del .build-deps

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
                echo 'opcache.memory_consumption=128'; \
                echo 'opcache.interned_strings_buffer=8'; \
                echo 'opcache.max_accelerated_files=4000'; \
                echo 'opcache.revalidate_freq=2'; \
                echo 'opcache.fast_shutdown=1'; \
                echo 'opcache.enable_cli=1'; \
        } > /usr/local/etc/php/conf.d/opcache-recommended.ini

VOLUME /var/www/html

# setting php uploading parameters
COPY uploads.ini /usr/local/etc/php/conf.d/uploads.ini



