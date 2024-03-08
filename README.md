# mongodb puppet module

[![Build Status](https://github.com/voxpupuli/puppet-mongodb/workflows/CI/badge.svg)](https://github.com/voxpupuli/puppet-mongodb/actions?query=workflow%3ACI)
[![Release](https://github.com/voxpupuli/puppet-mongodb/actions/workflows/release.yml/badge.svg)](https://github.com/voxpupuli/puppet-mongodb/actions/workflows/release.yml)
[![Puppet Forge](https://img.shields.io/puppetforge/v/puppet/mongodb.svg)](https://forge.puppetlabs.com/puppet/mongodb)
[![Puppet Forge - downloads](https://img.shields.io/puppetforge/dt/puppet/mongodb.svg)](https://forge.puppetlabs.com/puppet/mongodb)
[![Puppet Forge - endorsement](https://img.shields.io/puppetforge/e/puppet/mongodb.svg)](https://forge.puppetlabs.com/puppet/mongodb)
[![Puppet Forge - scores](https://img.shields.io/puppetforge/f/puppet/mongodb.svg)](https://forge.puppetlabs.com/puppet/mongodb)
[![License](https://img.shields.io/github/license/voxpupuli/puppet-mongodb.svg)](https://github.com/voxpupuli/puppet-mongodb/blob/master/LICENSE)

## Table of Contents

1. [Overview](#overview)
2. [Module Description - What does the module do?](#module-description)
3. [Setup - The basics of getting started with mongodb](#setup)
4. [Usage - Configuration options and additional functionality](#usage)
5. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
6. [Limitations - OS compatibility, etc.](#limitations)
7. [Development - Guide for contributing to the module](#development)

## Overview

Installs MongoDB on RHEL/Ubuntu/Debian from OS repo, or alternatively from
MongoDB community/enterprise repositories.

## Module Description

The MongoDB module manages mongod server installation and configuration of the
mongod daemon. For the time being it supports only a single MongoDB server
instance, without sharding functionality.

The MongoDB module also manages Ops Manager setup and the mongdb-mms daemon.

## Setup

### What MongoDB affects

* MongoDB package.
* MongoDB configuration files.
* MongoDB service.
* MongoDB client.
* MongoDB sharding support (mongos)
* MongoDB apt/yum repository.
* Ops Manager package.
* Ops Manager configuration files.

### Beginning with MongoDB

If you just want a server installation with the default options you can run
`include mongodb::server`. If you need to customize configuration
options you need to do the following:

```puppet
class {'mongodb::server':
  port    => 27018,
  verbose => true,
}
```

For Red Hat family systems, the client can be installed in a similar fashion:

```puppet
class {'mongodb::client':}
```

Note that for Debian/Ubuntu family systems the client is installed with the
server. Using the client class will by default install the server.

If one plans to configure sharding for a Mongo deployment, the module offer
the `mongos` installation. `mongos` can be installed the following way :

```puppet
class {'mongodb::mongos' :
  configdb => ['configsvr1.example.com:27018'],
}
```

Although most distros come with a prepacked MongoDB server, you may prefer to
use a more recent version. To install MongoDB from the community repository:

```puppet
class {'mongodb::globals':
  manage_package_repo => true,
  version             => '3.6',
}
-> class {'mongodb::client': }
-> class {'mongodb::server': }
```

If you don't want to use the MongoDB software repository or the OS packages,
you can point the module to a custom one.
To install MongoDB from a custom repository:

```puppet
class {'mongodb::globals':
  manage_package_repo => true,
  repo_location => 'http://example.com/repo'
}
-> class {'mongodb::server': }
-> class {'mongodb::client': }
```

Having a local copy of MongoDB repository (that is managed by your private modules)
you can still enjoy the charms of `mongodb::params` that manage packages.
To disable managing of repository, but still enable managing packages:

```puppet
class {'mongodb::globals':
  manage_package_repo => false,
  manage_package      => true,
}
-> class {'mongodb::server': }
-> class {'mongodb::client': }
```

## Usage

Most of the interaction for the server is done via `mongodb::server`. For
more options please have a look at [mongodb::server](REFERENCE.md#mongodb--server).
Also in this version we introduced `mongodb::globals`, which is meant more
for future implementation, where you can configure the main settings for
this module in a global way, to be used by other classes and defined resources.
On its own it does nothing.

### Create MongoDB database

To install MongoDB server, create database "testdb" and user "user1" with password "pass1".

```puppet
class {'mongodb::server':
  auth => true,
}

mongodb::db { 'testdb':
  user          => 'user1',
  password_hash => 'a15fbfca5e3a758be80ceaf42458bcd8',
}
```

Parameter 'password_hash' is hex encoded md5 hash of "user1:mongo:pass1".
Unsafe plain text password could be used with 'password' parameter instead of 'password_hash'.

### Ops Manager

To install Ops Manager and have it run with a local MongoDB application server do the following:

```puppet
class {'mongodb::opsmanager':
  opsmanager_url        => 'http://opsmanager.yourdomain.com'
  mongo_uri             => 'mongodb://yourmongocluster:27017,
  from_email_addr       => 'opsmanager@yourdomain.com',
  reply_to_email_addr   => 'replyto@yourdomain.com',
  admin_email_addr      => 'admin@yourdomain.com',
  $smtp_server_hostname => 'email-relay.yourdomain.com'
}
```

The default settings will not set useful email addresses. You can also just run `include mongodb::opsmanager`
and then set the emails later.

## Ops Manager Usage

Most of the interaction for the server is done via `mongodb::opsmanager`. For
more options please have a look at [mongodb::opsmanager](REFERENCE.md#mongodb--opsmanager).

## Reference

see [REFERENCE.md](REFERENCE.md)

## Limitations

This module has been tested on:

* Debian 7.* (Wheezy)
* Debian 6.* (squeeze)
* Ubuntu 12.04.2 (precise)
* Ubuntu 10.04.4 LTS (lucid)
* RHEL 5/6/7
* CentOS 5/6/7

For a full list of tested operating systems please have a look at the [.nodeset.xml](https://github.com/voxpupuli/puppet-mongodb/blob/master/.nodeset.yml) definition.

This module should support `service_ensure` separate from the `ensure` value on `Class[mongodb::server]` but it does not yet.

## Development

This module is maintained by [Vox Pupuli](https://voxpupuli.org/). Voxpupuli
welcomes new contributions to this module, especially those that include
documentation and rspec tests. We are happy to provide guidance if necessary.

Please see [CONTRIBUTING](.github/CONTRIBUTING.md) for more details.

### Authors

* Puppetlabs Module Team
* Voxpupuli Team

We would like to thank everyone who has contributed issues and pull requests to this module.
A complete list of contributors can be found on the
[GitHub Contributor Graph](https://github.com/voxpupuli/puppet-mongodb/graphs/contributors)
for the [puppet-mongodb module](https://github.com/voxpupuli/puppet-mongodb).
