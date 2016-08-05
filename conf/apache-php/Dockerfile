FROM php:7-apache

# Install PHP plugins
RUN apt-get update && apt-get install -y \
  libfreetype6-dev \
  libjpeg62-turbo-dev \
  libpng12-dev \
  libmcrypt-dev \
&& docker-php-ext-install \
  pdo_mysql \
  mysqli \
  mbstring \
  gd \
  iconv \
  mcrypt \
&& rm -rf /var/lib/apt/lists/*

# Install Composer
#RUN cd $HOME &&\
#    curl -sS https://getcomposer.org/installer | php &&\
#    chmod +x composer.phar &&\
#    mv composer.phar /usr/local/bin/composer

# Install phing, PHPUnit and PHPDocx with user "dev"
#USER dev
#RUN composer global require "phing/phing=2.*" &&\
#    composer global require "phpunit/phpunit=*" &&\
#    composer global require 'theseer/phpdox=@stable'
#ENV PATH $PATH:$COMPOSER_HOME/vendor/bin/
#USER root

# Inject config files
COPY php.ini /usr/local/etc/php/

RUN rm -rf /etc/apache2/sites-enabled/* && rm -rf /etc/apache2/sites-available/*
COPY vhosts.conf /etc/apache2/sites-available/000-vhost.conf
RUN a2ensite 000-vhost.conf


# Expose http and https ports
EXPOSE 80 443

# CMD ["bash"]
CMD ["apache2-foreground"]
