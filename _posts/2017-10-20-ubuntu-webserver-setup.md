---
title: Ubuntu Webserver Setup
layout: post
comments: true
published: true
description: 
keywords: 
---

This tutorial shows you how to install a LAMP stack under **Ubuntu 18.04 LTS (Bionic Beaver)**.

## Contents

* [Requirements](#requirements)
* [Install Apache, PHP, MySQL](#install-apache-php-mysql)
  * [Test Apache](#test-apache)
  * [Create a PHP info page](#create-a-php-info-page)
* [Enable apache mod_rewrite](#enable-apache-mod_rewrite)
* [Set permissions](#set-permissions)
* [Setup a SFTP server](#setup-a-sftp-server)
* [Change mysql root password](#change-mysql-root-password)
* [Install composer](#install-composer)

## Requirements

* Ubuntu 18.04 LTS (Bionic Beaver) [ISO](http://cdimage.ubuntu.com/daily-live/current/bionic-desktop-amd64.iso) 
from the [download page](http://cdimage.ubuntu.com/daily-live/current/)

## Install Apache, PHP, MySQL

All required components can be installed from the official package sources. 
Here you can install other server components besides Apache, MySQL and PHP.

Expert info: During the installation, a password for the database administrator root is always requested. Please choose this password carefully and keep it safe. And please do not confuse the database administrator with the system administrator account root. They are two completely different users, even if the name is identical.

```bash
sudo apt-get update
sudo apt-get install vim -y
sudo apt-get install zip unzip -y
```

```bash
sudo apt-get install apache2 -y
sudo apt-get install mysql-server mysql-client libmysqlclient-dev -y
sudo apt-get install libapache2-mod-php7.2 php7.2 php7.2-mysql php7.2-sqlite -y
sudo apt-get install php7.2-mbstring php7.2-curl php7.2-intl php7.2-gd php7.2-zip php7.2-bz2 -y
sudo apt-get install php7.2-dom php7.2-xml php7.2-soap -y
```

### Test Apache

For a first test with a web browser, visit http://localhost or http://127.0.0.1

The following should be displayed:
```
It works!

This is the default web page for this server.

The web server software is running but no content has been added, yet.
```

If this page is not displayed, Apache probably hasn't started yet. 
In this case, start the web server with the following command:

```bash
sudo service apache2 start 
```

### Create a PHP info page

```bash
cd /var/www/html/
sudo echo "<?php phpinfo();" > phpinfo.php
```

Open: http://localhost/phpinfo.php

## Enable apache mod_rewrite

To enable the rewrite modul, run:

```bash
sudo a2enmod rewrite
sudo a2enmod actions
```

If you plan on using mod_rewrite in `.htaccess` files, you also need to enable the use of `.htaccess`
files by changing `AllowOverride None` to `AllowOverride All`.

```bash
sed -i '170,174 s/AllowOverride None/AllowOverride All/g' /etc/apache2/apache2.conf
```

Finally, the configuration of Apache has to be reloaded.

```bash
sudo service apache2 restart
```

*Optional:* To check if mod_rewrite is installed correctly, download an run this script:

[Webserver and PHP configuration tester](https://gist.github.com/odan/64606e3668eac0e13afc)

```bash
cd /var/www/html/
sudo php -r "copy('https://gist.githubusercontent.com/odan/64606e3668eac0e13afc/raw/d0fcc8efadaab6c197f3c63f09e4232cb599c2b2/webserver-test.php', 'webserver-test.php');"
sudo php webserver-test.php
```

## Set permissions

Make `/var/www/` writable without sudo privileges.

Set owner for `/var/www/` to www-data (recursive)

```bash
sudo chown -R www-data /var/www/
```

The next command adds a attribute (recursive) which will keep new files 
and directories within `/var/www/` having the same group permissions.

```bash
sudo chmod -R g+s /var/www/
```

Test the result

```bash
ls -l
```

**Note:** On a production server it's recommended to chown `www-data` to `/var/www/another_folder/`, not to `/var/www/`.

## Change mysql root password

Start mysql

```bash
sudo service mysql start
```

Change the password:

```bash
sudo mysql -u root --password="" -e "update mysql.user set authentication_string=password(''), plugin='mysql_native_password' where user='root';"
sudo mysql -u root --password="" -e "flush privileges;"
```

**Note** On a production server you should now run `sudo mysql_secure_installation` to 
improve the security of your MySQL installation.

Read more:

* https://dev.mysql.com/doc/refman/5.7/en/mysql-secure-installation.html
* https://mariadb.com/kb/en/library/mysql_secure_installation/

## Install composer

```bash
cd /var/www/
sudo php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
sudo php -r "unlink('composer-setup.php');"
sudo composer
```

## Install adminer

```bash
cd /var/www/html/
sudo php -r "copy('https://github.com/vrana/adminer/releases/download/v4.6.2/adminer-4.6.2.php', 'adminer.php');"
```

Open: http://localhost/adminer.php

## Setup a SFTP server

```bash
sudo apt-get install openssh-server
```

## Read more

* [How to setup a restricted SFTP server on Ubuntu?](https://askubuntu.com/questions/420652/how-to-setup-a-restricted-sftp-server-on-ubuntu)
