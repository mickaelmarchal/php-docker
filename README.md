# php-docker
Dev platform for PHP / MySQL using Docker. Contains :

* Apache 2.4
* PHP 7.0
* MySQL 5.7
* phpMyAdmin

This is a work in progress (see TODO below).

## Features
* Embeds PHP modules `pdo_mysql`, `mysqli`, `mbstring`, `gd`, `iconv`, `mcrypt`
* Uses Apache VirtualHosts to host multiple projects

## Quickstart

Just run `docker-compose up -d` to start the platform.


#### VirtualHosts
Apache will serve files located in the `src/project1` subdirectory on http://project1.local:12345 and files in `src/project2` on http://project2.local:12345.

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

phpMyAdmin can be reached on http://localhost:12346

MySQL credentials :
* username: `root`
* password: `password`

## TODO

* ~~Apache VirtualHosts~~
* ~~SSL~~
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
