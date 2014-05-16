---
layout: docs
title: Installing using Vagrant
---

This guide shows you how to setup a Vagrant development environment. Alternatively you can follow the [manual install instructions]({{ site.baseurl }}/docs/install/).

### Overview

Vagrant provides any easy method to setup virtual development environments, for further information see [their website](http://www.vagrantup.com).

The basic process is to create a "base" vm, and then "provision" it with the software packages and setup needed. There are several ways to do this, including Chef and Puppet, but these instructions simply provide a shell script to keep things simple and avoid the need to understand further complicated systems. The supplied scripts will create you a Vagrant vm based on the server edition of Ubuntu 12.04 LTS that contains everything you need to work on PopIt.

### 1. Install vagrant

You'll need to install [VirtualBox](http://www.virtualbox.org/wiki/Downloads) then [Vagrant](http://downloads.vagrantup.com/).

### 2. Get the code

From your newly cloned `popit` directory, move `popit/config/Vagrantfile` and `popit/config/provision.sh` out, so that they're in the same directory as the `popit` repository, e.g.:

    mkdir popit_root
    cd popit_root
    git clone https://github.com/mysociety/popit
    cd popit
    cp config/Vagrantfile ../
    cp config/provision.sh ../

### 3. Provision virtual machine

Start the virtual machine and run the provisioning script using the following command.

    vagrant up

This will probably take 10 minutes or so depending on your internet connection speed. Vagrant will run `provision.sh` to configure the box correctly.

### 4. Log in

When the install process has finished, you should be able to SSH into your new development environment. You'll find the code for PopIt in `/vagrant/popit`. Then you'll need to install the required modules and generate the css.

    vagrant ssh
    cd /vagrant/popit
    cp config/development.js-example config/development.js
    make

### 5. Start the PopIt server

From the `/vagrant/popit` directory in the virtual machine you can start the PopIt server.

    npm start

### 6. Done!

You can now edit your code from your editor of choice on your host computer, and view the changes live at `http://www.127.0.0.1.xip.io:3000`, with all the server config and versioning nicely contained in a virtual environment.
