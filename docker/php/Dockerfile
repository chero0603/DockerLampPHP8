FROM php:8.0-fpm

ARG DOCKER_UID=1000

RUN usermod -o -u ${DOCKER_UID} www-data

RUN chown -R www-data:${DOCKER_UID} /var/www

COPY www-original.conf /etc/php-fpm.d/www.conf

COPY ./php.ini /usr/local/etc/php/php.ini
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN apt-get update \
  && apt-get install -y zlib1g-dev mariadb-client vim libzip-dev \
  && docker-php-ext-install zip pdo_mysql

RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
  && docker-php-ext-configure gd --with-freetype --with-jpeg \
  && docker-php-ext-install -j$(nproc) gd

RUN pecl install xdebug \
  && docker-php-ext-enable xdebug

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezonecd

RUN apt-get update -qq
RUN apt-get install -y nodejs npm && npm install n -g && n lts

WORKDIR /var/www/html