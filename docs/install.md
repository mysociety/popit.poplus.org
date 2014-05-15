---
layout: docs
title: Installing PopIt
---

This page describes how to install PopIt manually.

### 1. Install prerequisite packages

You will need to install the following prerequisites in order to get PopIt running.

- [Node.js v0.10+](https://github.com/joyent/node/wiki/Installation)
- [MongoDB v2.0+](http://docs.mongodb.org/manual/installation/)
- [Elasticsearch v0.90](http://www.elasticsearch.org/guide/en/elasticsearch/reference/0.90/setup.html)
- [Sass v3.2.14](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#using_sass)

#### A. Mac OS X

Install either Homebrew or MacPorts (you might well have one already). Then use one of the commands below to install a few packages that PopIt needs.

##### i. Homebrew

    brew install git node mongodb graphicsmagick elasticsearch

##### ii. MacPorts

    port install git-core npm mongodb graphicsmagick

You'll need to install [Elasticsearch](http://www.elasticsearch.org/download) manually if you're using MacPorts.

#### B. Debian/Linux

Before starting, update your package indexes:

    sudo apt-get update

First install Node.js:

    sudo apt-get install python-software-properties
    sudo add-apt-repository ppa:chris-lea/node.js
    sudo apt-get update
    sudo apt-get install python g++ make nodejs

Next install MongoDB:

    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv 7F0CEB10
    echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list
    sudo apt-get update
    sudo apt-get install mongodb-org

Then install Elasticsearch:

    wget -O - http://packages.elasticsearch.org/GPG-KEY-elasticsearch | sudo apt-key add -
    echo 'deb http://packages.elasticsearch.org/elasticsearch/0.90/debian stable main' | sudo tee /etc/apt/sources.list.d/elasticsearch.list
    sudo apt-get update
    sudo apt-get install openjdk-6-jre elasticsearch

Finally install other packages that PopIt depends on:

    sudo apt-get install ruby1.9.1 ruby1.9.1-dev git graphicsmagick unzip sendmail
    sudo gem install sass --version=3.2.14 --no-rdoc --no-ri
    sudo gem install compass --version=0.12.2 --no-rdoc --no-ri

### 2. Get the code

Fetch the latest version from GitHub:

    mkdir popit_root
    cd popit_root
    git clone https://github.com/mysociety/popit
    cd popit

### 3. Install node modules

PopIt uses several npm modules. To install them, run:

    make node-modules

### 4. Set up config

The settings for popit are defined in `config/<environment>.js`. There are defaults for development and production in `config/development.js-example` and `config/production.js-example` respectively.

    cp config/development.js-example config/development.js

### 5. Generate CSS

There is a make task which uses Compass and Sass to convert the .scss files to .css files

    make css

### 6. Run

    npm start

Your site should now be up and running. Check by going to [http://www.127.0.0.1.xip.io:3000/](http://www.127.0.0.1.xip.io:3000/) (or whatever your `<base_url>` is set to in the config).

### 7. Creating a new instance

1. To set up a new Popit instance, go to [http://www.127.0.0.1.xip.io:3000/instances/new](http://www.127.0.0.1.xip.io:3000/instances/new)
2. To get the email confirmation link, go to [http://www.127.0.0.1.xip.io:3000/_dev](http://www.127.0.0.1.xip.io:3000/_dev) and click the "Last email sent" link.

After confirmation, you should be able to see your new empty instance at `http://<instance>.127.0.0.1.xip.io:3000`.

### 8. Running tests

To run PopIt's test suite use the following:

    make test

This will run jshint, which performs a syntax check and also runs the unit tests.

## Appendix

### Some basic MongoDB for those new to it

It's handy to be able to poke about in the raw PopIt data to see what's there
and to get a feel for the structure of things.

The hosting database is called `<popit_prefix>__master`, and each instance
is given it's own database with the name `<popit_prefix>_<instance>`
(where `<popit_prefix>` is set in your config).

Assuming you've used the default settings in `development.js`, to connect to the hosting database and list all the instances:

    $ mongo popitdev__master
    > db.instances.find()

To connect to an instance database and list all the people:

    $ mongo popitdev_<instance-slug>
    > db.persons.find()

To list all the collections in a database:

    > show collections

The MongoDB site has a handy [getting started guide](http://docs.mongodb.org/manual/tutorial/getting-started/)
and [introduction](http://docs.mongodb.org/manual/core/crud-introduction/).

