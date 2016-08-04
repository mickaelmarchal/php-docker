# php-docker
Dev platform for php / mysql using docker. Contains :

* Apache 2.4
* PHP 7.0
* MySQL 5.7
* phpMyAdmin

This is a work in progress (see TODO below).


## Quickstart

Just run `docker-compose up -d` to start the platform.

Apache will serve files located in the `src` subdirectory on http://localhost:12345.

phpMyAdmin can be reached on http://localhost:12346

MySQL credentials :
* username: `root`
* password: `password`

## TODO

* Apache VirtualHosts
* Memcache
* SQL migrations
* Logs management
* Support for Multiple configurations (local, dev, CI)
* Integration with CI tools (PHPCS, PHPUnit, etc.)

## Reference

This project is based on :
* http://www.newmediacampaigns.com/blog/docker-for-php-developers
* https://github.com/naga3/docker-lamp
* http://javaetmoi.com/2015/07/plateforme-lamp-docker-compose/ [FR]
* https://github.com/EtreRe/docker-compose-baseimage
