FROM php:7.2-apache

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf
RUN a2enmod rewrite

RUN apt-get update \
    && apt-get install -y --no-install-recommends vim curl debconf git apt-transport-https apt-utils \
    build-essential locales acl mailutils wget zip unzip zlib1g-dev libicu-dev g++ libpng-dev libpcre3-dev \
    fontconfig libfreetype6 libjpeg62-turbo libx11-6 libxcb1 libxext6 libxrender1 xfonts-75dpi xfonts-base \
    libldb-dev libldap2-dev gnupg gnupg1 gnupg2  g++ libssl1.0 libc6;

RUN rm -rf /var/lib/apt/lists/*
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    echo "fr_FR.UTF-8 UTF-8" >> /etc/locale.gen && \
    locale-gen

RUN apt-get update
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN curl https://packages.microsoft.com/config/debian/9/prod.list > /etc/apt/sources.list.d/mssql-release.list
RUN apt-get update
RUN ACCEPT_EULA=Y apt-get install -y unixodbc odbcinst1debian2 unixodbc-dev msodbcsql17 mssql-tools
RUN pecl install sqlsrv
RUN docker-php-ext-enable sqlsrv
RUN pecl install pdo_sqlsrv
RUN docker-php-ext-enable pdo_sqlsrv

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN apt-get update && apt-get install -y yarn
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash -
RUN apt-get update && apt-get install -y nodejs

RUN apt-get update && apt-get install -y libc-client-dev libkrb5-dev && rm -r /var/lib/apt/lists/*
RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl
RUN docker-php-ext-install imap

RUN docker-php-ext-configure intl
RUN docker-php-ext-install pdo pdo_mysql gd opcache intl zip calendar ldap
RUN pecl install apcu-5.1.5 && docker-php-ext-enable apcu

COPY wkhtmltox_0.12.5-1.stretch_amd64.deb /tmp
RUN dpkg -i /tmp/wkhtmltox_0.12.5-1.stretch_amd64.deb

COPY php.ini /usr/local/etc/php/php.ini

RUN curl -sSk https://getcomposer.org/installer | php -- --disable-tls && \
   mv composer.phar /usr/local/bin/composer

RUN addgroup --system gponty --gid 1000 && adduser --system gponty --uid 1000 --ingroup gponty

USER gponty
RUN /usr/local/bin/composer global require "banago/phploy"
ENV PATH /home/gponty/.composer/vendor/bin:$PATH

USER root

RUN pecl install xdebug
RUN docker-php-ext-enable xdebug

RUN echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_connect_back=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini;



WORKDIR /var/www/
