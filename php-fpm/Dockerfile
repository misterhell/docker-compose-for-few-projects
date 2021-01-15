FROM php:7.2-fpm

ARG USER_ID
ARG GROUP_ID

COPY xdebug.ini /tmp/xdebug.ini

RUN apt-get update && apt-get install -y \
    git \
    curl \
    wget \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libzip-dev \
    libpq-dev \
    postgresql \
    libpng-dev zlib1g-dev libicu-dev g++ libmagickwand-dev --no-install-recommends libxml2-dev \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-install mbstring zip xml gd pdo_mysql pdo_pgsql pgsql dom intl \
    && pecl install imagick && docker-php-ext-enable imagick \
    && pecl install mcrypt-1.0.3 && docker-php-ext-enable mcrypt

#INSTALL APCU
ARG PHP_FPM_EXT_APCU
RUN if [ $PHP_FPM_EXT_APCU = "true" ] ; then \
    pecl install apcu && docker-php-ext-enable apcu \
    && echo "extension=apcu.so" > /usr/local/etc/php/php.ini \
    && echo "apc.enable_cli=1" > /usr/local/etc/php/php.ini \
    && echo "apc.enable=1" > /usr/local/etc/php/php.ini; \
    fi ;


#INSTALL XDEBUG
ARG PHP_FPM_XDEBUG
RUN if [ $PHP_FPM_XDEBUG = "true" ] ; then \
    pecl install xdebug; \
    docker-php-ext-enable xdebug; \
    cat /tmp/xdebug.ini >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    fi ;


ADD ./php.ini /usr/local/etc/php/php.ini

RUN wget https://getcomposer.org/installer -O - -q \
    | php -- --install-dir=/bin --filename=composer --quiet


RUN usermod -u ${USER_ID} www-data && groupmod -g ${GROUP_ID} www-data

WORKDIR /var/www

USER "${USER_ID}:${GROUP_ID}"

CMD ["php-fpm"]