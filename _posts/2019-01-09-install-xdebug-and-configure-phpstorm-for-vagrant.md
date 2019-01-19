---
title: Install Xdebug and configure PhpStorm for Vagrant
layout: post
comments: true
published: false
description: 
keywords: google, notifications
---


## Intro

todo

## Install xdebug

I'm using Ubuntu, Apache and PHP 7.2 within my vagrant box.

```bash
# install the php xdebug extension
sudo apt-get install php-xdebug

# add the xdebug settings to php.ini
sudo echo 'xdebug.remote_port=9000' >> /etc/php/7.2/apache2/php.ini
sudo echo 'xdebug.remote_enable=1' >> /etc/php/7.2/apache2/php.ini
sudo echo 'xdebug.remote_connect_back=1' >> /etc/php/7.2/apache2/php.ini

# restart apache
sudo service apache2 restart
```

## Configure PhpStorm for Vagrant

Now add anew port forwarding rule to your `Vagrantfile`.
This rulle will redirect all tcp-ip connections from localhost:9001 to the vm internal port 9000.
I don't use a mapping from port 9000 to 9000 to prevent conflicts with the local host.

```vagrantfile
config.vm.network "forwarded_port", guest: 9000, host: 9001
```

The complete `Vagrantfile` looks like this:

```vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 9000, host: 9001
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.customize ['modifyvm', :id, '--cableconnected1', 'on']
  end
end
```

## Configure PhpStorm for Vagrant

* Click "Edit Configuration"
* Add Remotedebugging
* Host 127.0.0.1
* Port: 9001


