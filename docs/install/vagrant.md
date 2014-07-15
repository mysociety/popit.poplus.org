---
layout: docs
title: Installing using Vagrant
---

This guide shows you how to setup a Vagrant development environment. Alternatively you can follow the [manual install instructions]({{ site.baseurl }}/docs/install/).

### Overview

Vagrant provides any easy method to setup virtual development environments, for further information see [their website](http://www.vagrantup.com).

The basic process is to create a "base" vm, and then "provision" it with the software packages and setup needed. There are several ways to do this, including Chef and Puppet, but these instructions simply provide a shell script to keep things simple and avoid the need to understand further complicated systems. The supplied scripts will create you a Vagrant VM based on the server edition of Ubuntu 12.04 LTS that contains everything you need to work on PopIt.

### 1. Install VirtualBox and Vagrant

You'll need to install [VirtualBox](http://www.virtualbox.org/wiki/Downloads) then [Vagrant](http://downloads.vagrantup.com/).

### 2. Get the code

Clone the [PopIt repository](https://github.com/mysociety/popit) from GitHub:

    git clone https://github.com/mysociety/popit
    cd popit

### 3. Provision virtual machine

Start the virtual machine and run the provisioning script using the following command.

    vagrant up

This will probably take 10 minutes or so depending on your internet connection speed. Vagrant will run `config/provision.sh` to configure the box correctly.

### 4. Log in

When the install process has finished, you should be able to SSH into your new development environment. You'll find the code for PopIt in `/vagrant`.

    vagrant ssh

### 5. Start the PopIt server

From the `/vagrant` directory in the virtual machine you can start the PopIt server.

    npm start

If you want the server to restart when you change a file, use the following command:

    ./node_modules/.bin/supervisor --ignore public server.js

### 6. Done!

You can now edit your code from your editor of choice on your host computer, and view the changes live at http://www.127.0.0.1.xip.io:3000/, with all the server config and versioning nicely contained in a virtual environment.

### 7. (optional) Run the tests

To run the test suite for PopIt use the following command:

    npm test

## Customizing the Vagrant instance

There are some options which allow changing some aspects of the virtual machine.

The options can be set either by prefixing the vagrant command, or by exporting to the environment.

Note that you will also need to set these options when you run `vagrant reload`.

- `POPIT_VAGRANT_PORT` - Change the host port the application listens on (default 3000).
- `POPIT_VAGRANT_NFS` - Set to '1' to enable [NFS](http://docs.vagrantup.com/v2/synced-folders/nfs.html) for faster shared folders (default off).
- `POPIT_VAGRANT_MEMORY` - Change the amount of memory assigned to the VM in MB (default 1024MB).

#### Prefixing the command

    POPIT_VAGRANT_MEMORY=2048 vagrant up

#### Exporting to the environment

    export POPIT_VAGRANT_MEMORY=2048
    vagrant up
    vagrant reload

Both have the same effect, but exporting will retain the variable for the duration of your shell session.
