FROM php:7.1-apache

# Install PHP plugins. Add you own here.
RUN apt-get update && apt-get install -y \
  libfreetype6-dev \
  libjpeg62-turbo-dev \
  libpng12-dev \
  unzip \
  git \
&& docker-php-ext-install \
  pdo_mysql \
  mysqli \
  gd \
&& rm -rf /var/lib/apt/lists/*

# Install Composer
RUN cd $HOME &&\
    curl -sS https://getcomposer.org/installer | php &&\
    chmod +x composer.phar &&\
    mv composer.phar /usr/local/bin/composer

RUN composer global require "phpunit/phpunit=*" &&\
    composer global require 'theseer/phpdox=@stable'
ENV PATH $PATH:$COMPOSER_HOME/vendor/bin/


# Install PHP config
COPY php.ini /usr/local/etc/php/

# Install Apache SSL config and certificates
RUN a2enmod ssl
RUN mkdir /etc/apache2/ssl
COPY ssl-cert.key /etc/apache2/ssl/ssl-cert.key
COPY ssl-cert.crt /etc/apache2/ssl/ssl-cert.crt

# Install any additional Apache module here
# RUN a2enmod headers

# Install Apache virtual hosts
RUN rm -rf /etc/apache2/sites-enabled/* && rm -rf /etc/apache2/sites-available/*
COPY dev-tools.conf /etc/apache2/sites-available/000-dev-tools.conf
COPY vhosts/*.conf /etc/apache2/sites-available/
RUN a2ensite *.conf

# Expose http and https ports
EXPOSE 80 443

CMD ["apache2-foreground"]
