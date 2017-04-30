# php-docker
Dev platform for PHP / MySQL using Docker.

This is the development server I use for all my personal and professional projects.
It allows me to set-up PHP any project really quickly.

This is a work in progress (see TODO below). New features will be added according to my needs,
but do not hesitate to fork the project and add your own!


## 1. Features
This environment contains :
* PHP 7.1 running on Apache 2.4
* MySQL 5.7
* phpMyAdmin
* Mailcatcher

The platform is compatible with Linux, Mac and Windows hosts and is based on official Docker images
when possible, for better support.

Other features:
* PHP modules included: `pdo_mysql`, `mysqli`, `mbstring`, `gd` (add you own easily).
* Uses Apache VirtualHosts to host multiple projects on different domains
* Supports HTTPS projects, self-signed SSL certificate included
* Includes a home page to quickly access dev tools


## 2. Quick Start

### 2.1. Set-up
Make sure that Docker is installed (on Mac / Windows, use _Docker for Mac_ / _Docker for Windows_).


Run `docker-compose up -d` from you command-line interface to start the platform.
For users of `docker-machine` (Windows Home edition), see below.

Once started, just browse to [http://localhost](http://localhost).


### 2.2. Development tools

#### 2.2.1. phpMyAdmin
Browse to [http://localhost:8888](http://localhost:8888) and log in with:
* username: `root`
* password: `password`


#### 2.2.2. MySQL
From your PHP project, use the following credentials to connect to MySQL:

* host: `mysql`
* port: `3306`
* connection type: `TCP`
* username: `root`
* password: `password`


#### 2.2.3. Mailcatcher
Configure your PHP mailer to send emails to SMTP server situated at `localhost:1025`. All emails will
be caught by Mailcatcher and can be viewed on [http://localhost:1080](http://localhost:1080).


### 2.3.Adding a project
To add a new project:

1. Add the hostname of your project to your `/etc/hosts` file:
   * __On Mac / Linux:__ log-in as root and edit the file `/etc/hosts`.
   * __On Windows:__ open notepad as an Administrator, and edit the file `C:\Windows\System32\Drivers\etc\hosts`.
                                                                 
   Add the following line:
   ```
   127.0.0.1   myproject.local
   ```
2. Create an Apache VirtualHost file `conf/php/vhosts/myproject.conf`:
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
3. Add the configuration to `docker-compose.yml`:
   ```
     php: 
       volumes:
         - ./relative-path-to/myproject:/var/www/html/my-project
   ```
4. Rebuild and restart containers:
   ```
   docker-compose down && docker-compose build && docker-compose up -d
   ```
5. Your project is available on [http://myproject.local](http://myproject.local).
   
   
## 3. In-depth documentation

### 3.1. Shell access
In the root directory, just run:
```
docker-compose run php bash
```

### 3.2. Composer and other PHP Utilities
Composer is included, just run `composer` in the PHP container.

Other installed utilities:
* PHPUnit
* PHPDox


### 3.3. PHP modules and configuration
To install additional PHP modules, [see the official PHP docker image documentation](https://hub.docker.com/_/php/).
To modify the PHP configuration, edit the php.ini file in `conf/php/php.ini`.


### 3.4. MySQL configuration
To modify the SQL configuration, edit the my.cnf file in `conf/mysql/my.cnf`.


### 3.5. VirtualHosts and SSL
All *.conf files in `conf/php/vhosts/` will be added to Apache configuration.

The platform comes with a wildcard, self-signed SSL certificate matching `*.local`.
The certificate and the associated key are installed in `/etc/apache2/ssl/ssl-cert.key`
and `/etc/apache2/ssl/ssl-cert.crt`.

Here is a full example of a HTTP + HTTPS configuration:
```
<VirtualHost *:80>
    ServerName myproject.local
    ServerAdmin webmaster@localhost

    DocumentRoot /var/www/html/myproject
    DirectoryIndex index.php

    # For easier access to logs, put them in a directory shared with the host
    LogLevel warn
    ErrorLog /var/www/html/myproject/logs/error.log
    CustomLog /var/www/html/myproject/logs/access.log combined

    <Directory /var/www/html/myproject>
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>

<IfModule ssl_module>
    <VirtualHost _default_:443>
        ServerName myproject.local
        ServerAdmin webmaster@localhost

        SSLEngine on
        SSLCertificateFile      /etc/apache2/ssl/ssl-cert.crt
        SSLCertificateKeyFile /etc/apache2/ssl/ssl-cert.key

        DocumentRoot /var/www/html/myproject
        DirectoryIndex index.php

        # For easier access to logs, put them in a directory shared with the host
        LogLevel warn
        ErrorLog /var/www/html/myproject/logs/error.log
        CustomLog /var/www/html/myproject/logs/access.log combined

        <Directory /var/www/html/myproject>
            Options FollowSymLinks
            Require all granted
        </Directory>

    </VirtualHost>
</IfModule>
``` 

And do not forget to set up your `/etc/hosts` file:

```
127.0.0.1   myproject.local
```


### 3.6. Docker-toolbox users
The virtualization system is different from Docker for Windows. It might be necessary to make the following changes:

* When editing the hosts file, replace `127.0.0.1` by the IP address of the docker VM.
* Before running `docker-compose up -d`, do not forget to start the VM with `docker-machine start`.


### 3.7. Maintenance / Troubleshooting
* Stop the environment: `docker-compose down`
* Rebuild the environment (if the configuration of the environment has changed): `docker-compose build`
* Start environment: `docker-compose up -d`

Note: the commands above will not delete databases.

In case the containers are started but you are not able to connect to MySQL, it is possible that the database
got corrupted. The following procedure will reset the container and the database to its original state (you will
have to re-import all data).

```
docker-compose down
docker volume rm db
docker-compose up -d
```


## 4. TODO
* ~~Apache VirtualHosts~~
* ~~SSL~~
* ~~Mailcatcher~~
* ~~Comment and document everything~~
* Memcache
* SQL migrations support
* Logs management
* Integration with CI tools (PHPCS, PHPUnit, etc.)


## 5. References and suggested readings
This project is based on :
* http://www.newmediacampaigns.com/blog/docker-for-php-developers
* https://github.com/naga3/docker-lamp
* http://javaetmoi.com/2015/07/plateforme-lamp-docker-compose/ [FR]
* https://github.com/EtreRe/docker-compose-baseimage
* http://stackoverflow.com/questions/24827869/apache-in-docker-wont-deliver-sites
* https://github.com/AlexisNo/docker-apache-php-dev


## 6. MIT License
Copyright 2016 Mickaël Marchal

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated
documentation files (the "Software"), to deal in the Software without restriction, including without limitation
the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and
to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of
the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO
THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
