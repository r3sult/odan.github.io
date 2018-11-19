---
title: Vagrant with Ubuntu 18.04 Setup
layout: post
comments: true
published: true
description: 
keywords: 
---

## Requirements

* Windows
* Vagrant (+ VirtualBox)

### Setup

Create a `Vagrantfile` under c:\xampp\htdocs

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    vb.customize ['modifyvm', :id, '--cableconnected1', 'on']
  end
end
```

### Up and Running

```
cd c:\xampp\htdocs
vagrant up
vagrant ssh
```

### Install and configure Apache:

* <https://odan.github.io/2017/10/20/ubuntu-webserver-setup.html>


### Create a symlink

Source: /var/www/html<br>
Destination /vagrant

```
sudo rm -rf /var/www
sudo ln -s /vagrant/ /var/www/
```

Open: <http://localhost:8080>