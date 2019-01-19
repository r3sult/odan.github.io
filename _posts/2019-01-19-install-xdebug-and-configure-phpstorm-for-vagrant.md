---
title: Install Xdebug and configure PhpStorm for Vagrant
layout: post
comments: true
published: true
description: 
keywords: PhpStorm, Xdebug, Vagrant, php, debugger, linux
---


## Intro

I'm already aware that people use dockers these days, but I was dissatisfied with the performance on Mac. Even under Windows, many hacks and additional tools (docker-sync) are needed to work with Docker, and yet I wasn't satisfied. For these reasons, I'm back at Vagrant and describe here how to debug PhpStorm PHP applications that run in a (good old) Vagrant box.

## Install xdebug

I'm using [Ubuntu, Apache and PHP 7.2 within a vagrant box](https://odan.github.io/2017/10/20/ubuntu-webserver-setup.html).

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

* Open PhpStorm
* Click "Add configuration" (or Edit Configuration)

![image](https://user-images.githubusercontent.com/781074/51430761-dedb7900-1c1f-11e9-85b9-d45a0752cfa3.png)

* Click the `+` Button, then choos "PHP Remote Debug"
* Click the `...` Button and add a new Host `127.0.0.1`, Port: `9001`

![image](https://user-images.githubusercontent.com/781074/51430812-94a6c780-1c20-11e9-9b40-1ef70c0cd282.png)

* Enter a IDE key (session key): `PHPSTORM`
* Click OK
* Select the new configuration from the configuration menu
* Start the Xdebug session

![image](https://user-images.githubusercontent.com/781074/51430854-1696f080-1c21-11e9-8b62-f409878acb0b.png)

## Debugging

* Open the browser
* Add the xdebug cookie with the (PhpStorm Xdebug bookmarklets)[https://www.jetbrains.com/phpstorm/marklets/]
* Add a breakpoint

![image](https://user-images.githubusercontent.com/781074/51430916-f3b90c00-1c21-11e9-8d06-b1a97aee98f0.png)

* Start your program now. As soon as your program reaches the marked position, the debugger should stop at this point.



