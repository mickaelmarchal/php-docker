# php-docker
Dev platform for PHP / MySQL using Docker.

This is the development server I use for all my personal and professional projects.
It allows me to set-up PHP any project really quickly.


This environement contains :
* Apache 2.4
* PHP 7.1
* MySQL 5.7
* phpMyAdmin
* Mailcatcher

This is a work in progress (see TODO below).

## Features
* Embeds PHP modules `pdo_mysql`, `mysqli`, `mbstring`, `gd`, `iconv`
* Uses Apache VirtualHosts to host multiple projects
* Includes a home page to quickly access dev tools

## Quick Start

### Set-up
Make sure that Docker is installed (on Mac / Windows, use _Docker for Mac_ / _Docker for Windows_).

Just run `docker-compose up -d` from you command-line interface to start the platform.

Then, open your browser at [http://localhost](http://www.localhost).


### Adding a project

To add a new project:

1. Add the hostname of your project to your `/etc/hosts` file: 
   ```
   127.0.0.1   myproject.local
   ```
2. Create a Apache VirtualHost file in `conf/php/vhosts`:
   ```
   <VirtualHost *:80>
       ServerName myproject.local
       ServerAdmin webmaster@localhost
   
       DocumentRoot /var/www/html/myproject
       DirectoryIndex index.php

       <Directory /var/www/html/myproject>
           Options FollowSymLinks
           Require all granted
       </Directory>
   </VirtualHost>
   ```
3. Add the VirtualHost to  `conf/php/Dockerfile`:
   
4. Add the configuration to `docker-compose.yml`:
   ```
     php:
       
       # [...]
       
       volumes:
         - ./relative-path-to/myproject:/var/www/html/my-project

   ```
5. Rebuild and restart containers:
   ```
   docker-compose down && docker-compose build && docker-compose up -d
   ```
6. Your project is accessible on [http://myproject.local](http://myproject.local).
   
   
## In-depth documentation

### SSL sites
All *.conf files in `conf/php/vhosts/` will be added to Apache configuration.

The included `localhost.conf` file provides access to various dev tools.

For easy set
Here is a full example of a HTTP + HTTPS configuration:
```
<VirtualHost *:80>
    ServerName project1.local
    ServerAdmin webmaster@localhost

    DocumentRoot /var/www/html/project1
    DirectoryIndex index.php

    LogLevel warn
    ErrorLog /var/www/html/logs/error.log
    CustomLog /var/www/html/logs/access.log combined

    <Directory /var/www/html/project1>
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>


<IfModule ssl_module>
    <VirtualHost _default_:443>
        ServerName project1.local
        ServerAdmin webmaster@localhost

        SSLEngine on
        SSLCertificateFile      /etc/apache2/ssl/ssl-cert.crt
        SSLCertificateKeyFile /etc/apache2/ssl/ssl-cert.key

        DocumentRoot /var/www/html/project1
        DirectoryIndex index.php

#        LogLevel warn
#        ErrorLog /var/www/html/logs/error.log
#        CustomLog /var/www/html/access.log combined

        <Directory /var/www/html/project1>
            Options FollowSymLinks
            Require all granted
        </Directory>

    </VirtualHost>
</IfModule>
``` 


`Apache will serve files located in the `src/project1` subdirectory on http://project1.local:12345 and files in `src/project2` on http://project2.local:12345.

To reach those domains from the host, please ensure that your `/etc/hosts` file contains :

```
127.0.0.1   project1.local
127.0.0.1   project2.local
```

#### SSL Certificates

The platform comes with a wildcard, self-signed SSL certificate matching `*.local`. The certificate and the associated key are installed in `/etc/apache2/ssl/ssl-cert.key` and `/etc/apache2/ssl/ssl-cert.crt`.

Example: to use the certificate in a VirtualHost :

```
<VirtualHost _default_:443>
    ServerName project1.local

    SSLEngine on
    SSLCertificateFile    /etc/apache2/ssl/ssl-cert.crt
    SSLCertificateKeyFile /etc/apache2/ssl/ssl-cert.key

    #...

</VirtualHost>
```


#### phpMyAdmin

phpMyAdmin can be reached on http://localhost:8888

MySQL credentials :
* username: `root`
* password: `password`

## TODO

* ~~Apache VirtualHosts~~
* ~~SSL~~
* ~~Mailcatcher~~
* Memcache
* SQL migrations
* Logs management
* Support for Multiple configurations (local, dev, CI)
* Integration with CI tools (PHPCS, PHPUnit, etc.)
* Comment and document everything :)

## Reference

This project is based on :
* http://www.newmediacampaigns.com/blog/docker-for-php-developers
* https://github.com/naga3/docker-lamp
* http://javaetmoi.com/2015/07/plateforme-lamp-docker-compose/ [FR]
* https://github.com/EtreRe/docker-compose-baseimage
* http://stackoverflow.com/questions/24827869/apache-in-docker-wont-deliver-sites
* https://github.com/AlexisNo/docker-apache-php-dev
