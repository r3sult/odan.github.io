---
title: Accessing your Virtualbox Guest from your Host OS
date: 2017-10-29
layout: post
comments: true
published: true
description: 
keywords: 
---

As a developer you want to ping and access the webserver on your virtual machine.
This is a very simple solution to enable the bridge to the guest VM.

## Requirements

* VirtualBox (latest version)
* A guest operation system (e.g. Ubuntu)

## Setup

* Shut down all running VM's
* Right click on the VM > Change... > Network
* Open Tab: **Adapter 1**
* Enable the Adapter and select "NAT"

The next step is importand to make it work:

* Open Tab: **Adapter 2**
* Enable the adapter and select: "Host-only Adapter"
* Select Name: "VirtualBox Host-only Ethernet Adapter"
* Click at "Extended"
* Select the adapter: "Intel PRO/1000 MT Desktop..."
* Select the modus: "Allow all and host"
* Click on "Ok" to save all settings.

Yes, you have to enable two adapters at the same time to make it work. Realy.
You need a "NAT" and a "Host-only Adapter".

* Start the VM
* Open the terminal (with Ctrl+Alt+T)
* Enter: `ifconfig`
* Now you should see a local IP addresse like: `192.168.56.104`
* The IP address is dynamic an can be different on your VM

## Test

* Go back into your host machine
* Open the command line: `cmd`
* Ping the guest VM with the command: `ping 192.168.56.104`
* You should see the ping response
* If you have a webserver installed on the guest VM then open `http://192.168.56.104` in your browser to the hosted website.