FROM php:7.2-apache

MAINTAINER Temuri Takalandze(ABGEO) <takalandzet@gmail.com>

ARG APACHE_DOCUMENT_ROOT=/var/www/html/public
ENV APACHE_DOCUMENT_ROOT=${APACHE_DOCUMENT_ROOT}

# Set the locale
RUN apt-get clean && apt-get update && apt-get install -y locales
RUN locale-gen en_US.UTF-8

# Install tools
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install wget nano lnav git zip unzip sudo

# Install php requirements
RUN apt-get install -y zlib1g-dev libicu-dev \
    && docker-php-ext-install pdo_mysql intl zip opcache

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Apache configuration
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf
RUN a2enmod rewrite headers

# Configure mailserver

RUN apt-get install --no-install-recommends --assume-yes --quiet ca-certificates curl git \
    && rm -rf /var/lib/apt/lists/*

RUN curl -Lsf 'https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz' | tar -C '/usr/local' -xvzf -

ENV PATH /usr/local/go/bin:$PATH

RUN go get github.com/mailhog/mhsendmail
RUN cp /root/go/bin/mhsendmail /usr/bin/mhsendmail
RUN echo 'sendmail_path = /usr/bin/mhsendmail --smtp-addr mailhog:1025' > /usr/local/etc/php/php.ini

# Create new user
RUN useradd admin -s /bin/bash -m -g www-data

# Make sudo available without password
RUN echo "admin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Set web root diectory ownership to admin user
RUN sudo chown admin:www-data /var/www/html/ -R

# Clean-up installation.
RUN DEBIAN_FRONTEND=noninteractive apt-get autoclean && apt-get autoremove
