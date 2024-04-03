ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm

ARG UID
ARG GID

RUN groupadd -g $GID php || true
RUN useradd -rm -s /bin/bash -g php -u $UID -m php

RUN set -xe; \
    apt-get update -yqq \
    && pecl channel-update pecl.php.net \
    && apt-get install -yqq apt-utils zlib1g-dev g++ git libicu-dev libzip-dev zip unzip libpq-dev libxml2-dev htop

RUN docker-php-ext-install zip \
    && docker-php-ext-install pdo pdo_pgsql pgsql  \
    && docker-php-ext-install exif \
    && docker-php-ext-install xml \
    && docker-php-ext-install intl \
    && docker-php-ext-install sockets \
    && docker-php-ext-configure pcntl --enable-pcntl \
    && docker-php-ext-install pcntl opcache soap \ 
    && pecl install apcu \
    && docker-php-ext-enable apcu \
    && pecl install -o -f redis \
    && docker-php-ext-enable redis \
    && docker-php-ext-configure zip;

RUN curl -sLS https://deb.nodesource.com/setup_14.x | bash -  \ 
    && apt-get install -y nodejs

COPY ./php.ini /usr/local/etc/php/conf.d/php.ini
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini

ARG DEBUG_MODE

RUN if [ $DEBUG_MODE = true ] ; \
    then \
      pecl install xdebug && docker-php-ext-enable xdebug;

USER php

RUN mkdir "/home/php/bin"

WORKDIR /var/www

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/home/php/bin --filename=composer

RUN touch /home/php/.bashrc
RUN echo 'export PATH="/home/php/bin:$PATH"' >> /home/php/.bashrc

RUN mkdir "/home/php/log"
RUN touch "/home/php/log/fpm-php.www.log"

EXPOSE 9000
