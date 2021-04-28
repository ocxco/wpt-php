FROM laradock/php-fpm:latest-7.0

LABEL maintainer="xuechaoc<xuechaoc@qq.com>"

# Set Environment Variables
ENV DEBIAN_FRONTEND noninteractive

# Change application source from deb.debian.org to aliyun source
RUN sed -i 's/deb.debian.org/mirrors.aliyun.com/' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.aliyun.com/' /etc/apt/sources.list && \
    sed -i 's/security-cdn.debian.org/mirrors.aliyun.com/' /etc/apt/sources.list

# always run apt update when start and after add new source list, then clean up at end.
RUN set -xe; \
    apt-get update -yqq \
    && pecl channel-update pecl.php.net \
    && apt-get install -yqq apt-utils gnupg2  git libzip-dev zip unzip \
    && docker-php-ext-configure zip --with-libzip \
    # Install the zip extension
    && docker-php-ext-install zip \
    && php -m | grep -q 'zip' \
###########################################################################
# xDebug:
###########################################################################
  # Install the xdebug extension
    && pecl install xdebug-2.9.0 \
    && docker-php-ext-enable xdebug \
###########################################################################
# PHP REDIS EXTENSION
###########################################################################
    && pecl install -o -f redis-3.1.6 \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis \
###########################################################################
# Swoole EXTENSION
###########################################################################
    && pecl install swoole-4.3.5 \
    && docker-php-ext-enable swoole \
    && php -m | grep -q 'swoole' \
###########################################################################
# MongoDB:
###########################################################################
    && pecl install mongodb \
    && docker-php-ext-enable mongodb \
###########################################################################
# pcntl
###########################################################################
    && docker-php-ext-install pcntl \
###########################################################################
# bcmath:
###########################################################################
    && docker-php-ext-install bcmath \
###########################################################################
# PHP Memcached:
###########################################################################
    && pecl -q install memcached \
    && docker-php-ext-enable memcached \
###########################################################################
# Opcache:
###########################################################################
    && docker-php-ext-install opcache \
###########################################################################
# Mysqli Modifications:
###########################################################################
    && docker-php-ext-install mysqli \
###########################################################################
###########################################################################
# ImageMagick:
###########################################################################
    && apt-get install -yqq libmagickwand-dev imagemagick \
    && pecl install imagick-3.4.4 \
    && docker-php-ext-enable imagick \
    && php -m | grep -q 'imagick' \
###########################################################################
# APCU:
###########################################################################
    && pecl install apcu \
    && docker-php-ext-enable apcu \
###########################################################################
# YAML:
###########################################################################
    && apt-get install -yqq libyaml-dev \
    && pecl install yaml-2.0.4 \
    && docker-php-ext-enable yaml \
###########################################################################
# MySQL Client:
###########################################################################
    && apt-get -y install default-mysql-client \
###########################################################################
# ping:
###########################################################################
    && apt-get -y install inetutils-ping \
############################# Clean Up ####################################
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && rm /var/log/lastlog /var/log/faillog
###########################################################################
# Check PHP version:
###########################################################################
RUN set -xe; php -v | head -n 1 | grep -q "PHP 7.0."

########################## Composer ################################
RUN curl -fSkL --retry 3 https://getcomposer.org/download/2.0.13/composer.phar -o /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

RUN sed -i "s/xdebug.cli_color=0/xdebug.cli_color=1/" /usr/local/etc/php/conf.d/xdebug.ini

USER root


# Configure non-root user.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN groupmod -o -g ${PGID} www-data && \
    usermod -o -u ${PUID} -g www-data www-data

# Configure locale.
ARG LOCALE=POSIX
ENV LC_ALL ${LOCALE}

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000
