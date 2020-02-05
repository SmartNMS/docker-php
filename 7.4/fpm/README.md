# PHP 7.4 FPM
PHP 7.4 FPM for developer with some extensions and composer preinstalled.

## Dockerfile
```txt
FROM php:7.4-fpm

LABEL maintainer="wingsun97@qq.com"

# Tools and Libs
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    curl git zip unzip locales \
    locales \
    libwebp-dev libjpeg-dev libjpeg62-turbo-dev libpng-dev libfreetype6-dev \
    libzip-dev \
    libicu-dev \
    libmagickwand-dev \
    && apt-get clean

# Set locale to utf-8
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen
ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

# Install gd extension
RUN docker-php-ext-configure gd \
    --with-jpeg \
    --with-webp \
    --with-freetype \
    && docker-php-ext-install -j$(nproc) gd
    
# Install pdo_mysql bcmath zip intl extension
RUN docker-php-ext-install pdo_mysql bcmath zip intl

# Install imagick redis mongodb extension
RUN pecl install imagick && \
    pecl install redis && \
    pecl install mongodb && \
    docker-php-ext-enable imagick redis mongodb

# Set composer home directory
ENV COMPOSER_HOME /tmp/composer

# Install composer
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && composer self-update --clean-backups

# Clean up
RUN apt-get autoremove -y && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

```

## PHP Modules
```txt
bcmath
Core
ctype
curl
date
dom
fileinfo
filter
ftp
gd
hash
iconv
imagick
intl
json
libxml
mbstring
mongodb
mysqlnd
openssl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
readline
redis
Reflection
session
SimpleXML
sodium
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
zip
zlib
```
