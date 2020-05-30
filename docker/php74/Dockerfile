FROM php:7.4-fpm

MAINTAINER Daler Bahritdinov <dalerkbtut@gmail.com>

RUN apt-get update && apt-get install -y \
    git \
    unzip\
    libzip-dev\
    libmcrypt-dev\
    libc-client-dev\
    libkrb5-dev\
    libpcre3-dev\
    libpq-dev\
    libxml2-dev\
    libxslt1.1\
    libxslt1-dev\
    libmagickwand-dev\
    libmagickcore-dev\
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && rm -r /var/lib/apt/lists/*

# Install Postgre PDO
RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo_pgsql pgsql

# Required if need manipulate images
RUN docker-php-ext-configure exif \
    && docker-php-ext-install exif
    
RUN docker-php-ext-install bcmath \
        calendar\
        fileinfo\
        gettext\
        pcntl\
        sockets\
        xmlrpc\
        xmlwriter\
        xsl\
        opcache\
        zip\
        gd

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version

# Set timezone
RUN rm /etc/localtime
RUN ln -s /usr/share/zoneinfo/Europe/Moscow /etc/localtime
RUN "date"

# install xdebug
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug
RUN echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_connect_back=0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.idekey=\"PHPSTORM\"" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_host=docker.for.mac.localhost" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini    #this for mac
#RUN echo "xdebug.remote_host=host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini       #this for windows
#RUN echo "xdebug.remote_log=/tmp/xdebug.log" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini


ARG APP_USER_ID=1000
ARG APP_USERGROUP_ID=1000

RUN if [ $(grep ${APP_USERGROUP_ID} /etc/group) ]; then \
        OLD_NAME=$(grep ${APP_USERGROUP_ID} /etc/group | cut -d ':' -f1); \
        groupmod -n apps $(grep ${APP_USERGROUP_ID} /etc/group | cut -d ':' -f1); \
        echo "group $OLD_NAME renamed"; \
    else \
        groupadd -g ${APP_USERGROUP_ID} apps; \
    fi

RUN useradd -m -g ${APP_USERGROUP_ID} -s /bin/bash -u ${APP_USER_ID} apps && \
    usermod -aG apps www-data && \
    usermod -aG www-data apps && \
    echo "apps:123" | chpasswd

WORKDIR /var/www
