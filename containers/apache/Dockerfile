FROM php:8.3-apache

LABEL maintainer="Althaf K.A"

ARG APP_FOLDER
ARG APP_CRON
ENV APP_FOLDER=$APP_FOLDER
ENV APP_CRON=$APP_CRON

# Set the Drush version.
ENV DRUSH_VERSION 12.4.3

# Set the composer version.
ENV COMPOSER_VERSION 2.3.4

# Enable rewrite headers
RUN a2enmod rewrite headers

# Intall mysqli php extention
RUN docker-php-ext-install mysqli

# Setup Debian
RUN apt-get upgrade && apt-get update && ACCEPT_EULA=Y && apt-get install -y \
        unzip \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libwebp-dev \
        libjpeg-dev \
        libzip-dev \
        libgeoip-dev \
        libxml2-dev \
        libxslt-dev \
        libtidy-dev \
        libssl-dev \
        zlib1g-dev \
        libaio1 \
        apt-file \
        wget \
        vim \
        gnupg \
        gnupg2 \
        zip \
        git \
	      npm \
        nodejs \
        libonig-dev \
        build-essential \
        pkg-config \
        cron

# Set timezone to Asia/Kolkata for the OS
RUN ln -snf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime && echo "Asia/Kolkata" > /etc/timezone

# Set timezone for PHP
RUN echo "date.timezone = Asia/Kolkata" > /usr/local/etc/php/conf.d/timezone.ini

# Install PECLs
RUN pecl install timezonedb

RUN docker-php-ext-install -j$(nproc) calendar iconv bcmath xml mbstring pdo tidy gettext intl pdo pdo_mysql mysqli simplexml xml xsl xmlwriter zip opcache exif gd

RUN docker-php-ext-configure zip

RUN docker-php-ext-enable timezonedb

# Install SSL
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ssl-cert-snakeoil.key -out /etc/ssl/certs/ssl-cert-snakeoil.pem -subj "/C=AT/ST=Vienna/L=Vienna/O=Security/OU=Development/CN=example.com"
RUN a2ensite default-ssl
RUN a2enmod ssl

# Composer installation
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install Drush using Composer.
RUN composer global require drush/drush

# MySQL client installation
RUN apt-get update && apt-get install -y mariadb-client && rm -rf /var/lib/apt

# Mailhog
RUN curl --location --output /usr/local/bin/mhsendmail https://github.com/mailhog/mhsendmail/releases/download/v0.2.0/mhsendmail_linux_amd64 && \
    chmod +x /usr/local/bin/mhsendmail

RUN echo 'sendmail_path="/usr/local/bin/mhsendmail --smtp-addr=mailhog:1025 --from=no-reply@docker.dev"' > /usr/local/etc/php/conf.d/mailhog.ini

# Copy php.ini file to container
COPY /conf/php.ini /usr/local/etc/php/

# Drupal Console installation
RUN curl --location --output /bin/drupal https://drupalconsole.com/installer && chmod +x /bin/drupal

# Install GD
RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev
RUN docker-php-ext-configure gd --with-freetype --with-webp --with-jpeg
RUN docker-php-ext-install gd

# bz2 extenstion
RUN apt-get update && apt-get install -y libbz2-dev
RUN docker-php-ext-install bz2

# Install PECL Uploadprogress.
RUN pecl install uploadprogress && echo 'extension=uploadprogress.so' > /usr/local/etc/php/conf.d/uploadprogress.ini

# NPM install
RUN apt-get install nodejs -y

# Install soap
RUN docker-php-ext-install soap && docker-php-ext-enable soap

# Installing xdebug
RUN pecl install xdebug

COPY /xdebug/xdebug-debug.ini /home/xdebug/xdebug-debug.ini
COPY /xdebug/xdebug-default.ini /home/xdebug/xdebug-default.ini
COPY /xdebug/xdebug-off.ini /home/xdebug/xdebug-off.ini
COPY /xdebug/xdebug-profile.ini /home/xdebug/xdebug-profile.ini
COPY /xdebug/xdebug-trace.ini /home/xdebug/xdebug-trace.ini

ARG XDEBUG_MODES
ARG REMOTE_HOST="host.docker.internal"
ARG REMOTE_PORT=9003
ARG IDE_KEY="VSCODE"

ENV MODES=$XDEBUG_MODES
ENV CLIENT_HOST=$REMOTE_HOST
ENV CLIENT_PORT=$REMOTE_PORT
ENV IDEKEY=$IDE_KEY

# Installing GMP for web push notification
RUN apt-get update -y
RUN apt-get install -y libgmp-dev re2c libmhash-dev file
RUN ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/local/include/
RUN docker-php-ext-configure gmp
RUN docker-php-ext-install gmp

# Memcached installation.
RUN apt-get update && apt-get install --no-install-recommends -y memcached libmemcached-tools libzip-dev libmemcached-dev \
  && git clone https://github.com/php-memcached-dev/php-memcached /usr/src/php/ext/memcached \
  && cd /usr/src/php/ext/memcached \
  && docker-php-ext-configure memcached \
  && docker-php-ext-install memcached
ARG APP_MEMCACHED
ENV APP_MEMCACHED=$APP_MEMCACHED
COPY /memcached/memcached.conf /home/memcached/memcached.conf

# Entrypoint
COPY /entrypoint.sh /usr/local/bin/entrypoint
RUN chmod +x /usr/local/bin/entrypoint

# Cron
COPY /cron/cron_scheduler.txt /home/cron/cron_scheduler.txt
COPY /cron/cron.sh /home/cron/cron.sh

CMD ["bash", "entrypoint"]
