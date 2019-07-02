---
title: Linux Networking course notes
Created: 2016/07/02
Last modified: 2019/07/02 18:51:44
---

# Table of Contents

- [Table of Contents](#Table-of-Contents)
- [Notes](#Notes)
- [Puppet Master/Server Setup](#Puppet-MasterServer-Setup)
  - [Certificate Authority](#Certificate-Authority)
- [Puppet Agent Setup](#Puppet-Agent-Setup)
  - [Configure Puppet:](#Configure-Puppet)
  - [Approve Agent](#Approve-Agent)
- [Module Creation](#Module-Creation)
  - [Module breakdown](#Module-breakdown)
  - [Creating classes](#Creating-classes)
  - [Init.pp and Site.pp files](#Initpp-and-Sitepp-files)
  - [Creating configurations for the Module](#Creating-configurations-for-the-Module)
  - [Finalizing the Module](#Finalizing-the-Module)
- [Facter and params.pp](#Facter-and-paramspp)

# Notes
Interactive Course Guide <https://interactive.linuxacademy.com/diagrams/ThePuppetProject.html>

# Puppet Master/Server Setup

While Puppet can detect the hostname by default, with our playground servers, it needs a little help. Configurations used for initial Puppet Server startup and certificate generation are found at /etc/puppetlabs/puppet/puppet.conf. Specifically, we want to add the certname value to both the [main] and [master] sections:

```
[main]
certname = \<LABSERVERID\>.mylabserver.com

[master]
certname = \<LABSERVERID\>.mylabserver.com
vardir = /opt/puppetlabs/server/data/puppetserver
logdir = /var/log/puppetlabs/puppetserver
rundir = /var/run/puppetlabs/puppetserver
pidfile = /var/run/puppetlabs/puppetserver/puppetserver.pid
codedir = /etc/puppetlabs/code
```
## Certificate Authority

Puppet manages its own intermediate signing CA. Before we start the Puppet Server for the first time, we need to run the CA setup:

```shell
/opt/puppetlabs/bin/puppetserver ca setup
```
 
 The following command has to show the fingerprint:
```shell
puppetserver ca list
```
# Puppet Agent Setup

Change /etc/hosts to have private IP and  domain name with name "puppet". This will ensure that your puppet agent will recognize your puppet master properly.
```shell
172.31.124.197  pr3c0g2c.mylabserver.com puppet
```

## Configure Puppet:
The Puppet agent will automatically look for the server with the hostname puppet, so we actually have no configuration changes to make; if we wanted to change this, we would have to make changes within the /etc/puppetlabs/puppet/puppet.conf file, where all our Puppet agent-related configurations would be stored. For example, if we wanted to use our mylabserver hostname, we could update this to read:

```shell
[main]
server = LABSERVERID.mylabserver.com
```

We could also use the puppet-config command-line utility:

```shell
sudo /opt/puppetlabs/bin/puppet config set server LABSERVERID.mylabserver.com 
```

To see all configurations:

```shell
sudo  /opt/puppetlabs/bin/puppet config print
```

## Approve Agent

With our agent ready, we can now go ahead and confirm the certificate on the Puppet Server. Before we log in to the Server, however, let's output the agent's certificate fingerprint:

```shell
sudo puppet agent --fingerprint
```

Now, on the Puppet Server, let's see what we have to accept:

```shell
puppetserver ca list
```

If the fingerprints match, we can approve the server:

```shell
puppetserver ca sign --certname AGENTSERVERID.mylabserver.com
```

# Module Creation

The location "/etc/puppetlabs/code/environments" contains the environments available, and the environment use is enabled by default (only production is there as default)

Within our environment, we have three directories and a couple of files:

- **environment.conf** : Contains environmental settings; no need to touch this to adjust the production environment out-of-box
- **hiera.yaml**: Our Hiera configuration file that we'll take a look at this next video!
- **data**: The directory where we'll store our Hiera data, covered more in the next lesson
- **manifests**: Where our main manifest(s) are stored, and where we'll take our end-state configurations and map them to which servers we want to use them against
- **modules**: Directory where we'll write and store our end-state configuration file

To create a module one would use ```pdk new module <name_of_module>``` 

Install it like:
```shell
apt-get install pdk
```

After we're prompted with a series of questions, if we want to change any of the decisions later, we can edit the **metadata.jso** file

The following files will be created:;
- **data**: Just like in the above production directory, this data file stores Hiera information
- **examples**: Stores examples on how to use the module's classes and types
- **files**: Stores static files for nodes
- **manifests**: Stores our manifests, the files that build out our module
- **spec**: Spec tests for any plug-ins
- **tasks**: Contains any Puppet tasks, which allow us to provide ad-hoc commands and scripts to run on our nodes
- **templates**: Stores any templates, which can be used to generate content

## Module breakdown
Environment(production)
 < Module(nginx)
  < Manifest(install.pp)
   < Class(nginx)
    < Resource(package)
     < Atrributes(name,ensure)

## Creating classes

Some notes about class creation:

- Creating classes has to be done on the root of the new module directory, because of the **metadata.json** file.
- To create a class use ``` pdk new class install ```.
- On the install.pp file, we want to have names all in lower case and avoid dashes and other characters. This is because upper case is reserved to reference names instead of functions like Class and Service.
- Instead of tabs, always use two spaces.
- Single quotes always, unless we want to parse parameters for example.
- Between the attribute and values, we use *hash rocket* to map (=>). **They should be lined up, add spaces after the atrribute to line them up**.
- To check if the class is working fine: ```puppet parser validate install.pp```.

Example nginx class:
``` shell
  # Install nginx
  #
  # @summary Installs nginx
  #
  # @example
  #   include nginx::install
  class nginx::install {
    package { 'install_nginx':
      name   => 'nginx',
      ensure => 'present',
    }
  }
```

## Init.pp and Site.pp files

All modules must have an init.pp, which references all classes. From the module root dir, ```pdk new class <module_name>```

Example:
```
    class nginx {
      contain nginx::install
    }
```

On the environment, a site.pp (default manifest name) has to exist. This calls the module.

Example: 
```
    node pr3c0g3c.mylabserver.com{
      class {'nginx': }
    }
```
```Puppet agent -t``` to install modules on the agent

## Creating configurations for the Module

Some notes about configuring modules:

- Get or create configuration files on the Files directory.
- After ```pdk new class config```, use the *file* resource pointing to the configuration files.
	- *We can mention* ```Module/<configfile>``` *without the need to mention the files directory*
- To create a daemon for the module, a *service* class has to be created via ```pdk new class service```

## Finalizing the Module

We then complete the init.pp file with the new classes. We define the order the classes load via chaining arrows *->* and notifying arrows *~>* (runs only when the previous one made a change).

Run ```Puppet agent -t``` to pull everything.

# Facter and params.pp

*Facter* gathers system information that puppet can use, via a params.pp file.

Example: ```facter os.family``` will retrieve Linux Distribution




