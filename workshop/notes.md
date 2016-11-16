---
title: Configuration Management with Ansible
author: Paul Waring
geometry: margin=2.5cm
fontsize: 11pt
---

## Introduction

These notes outline the topics which will be covered in the workshop. They are
not a verbatim transcript of the workshop, but you should be able to use them
as a standalone resource.

If you are attending the workshop, you are welcome to read the notes beforehand
and send any questions or comments to the tutor at:
[paul@xk7.net](mailto:paul@xk7.net)

## The tutor

Paul Waring is a freelance developer and system administrator who works with a
range of businesses across the UK.

Before going freelance, Paul supported software for teaching at the University
of Manchester, and was the IT Director and Technical Manager at a wholesale
insurance broker.

Paul can be contacted via email at [paul@xk7.net](mailto:paul@xk7.net) and has
a website at [pwaring.com](https://www.pwaring.com).

## Audience

This workshop is aimed at people who are familiar with administering Linux
systems but are new to either configuration management or Ansible.

## Workshop requirements

Please see the separate requirements document for full details of the software
and hardware requirements for this workshop.

## Configuration Management

Back in the Dark Ages, system administrators would manage servers by logging
into each one in turn and editing configuration files. For single servers this
wasn't too problematic, but it didn't allow for quick rebuilding of servers, or
bringing up additional servers with identical setups (e.g. a farm of web
servers).

The simplest solution was to write a script, usually in Bash, which would
connect to servers using SSH and then run a series of commands to copy files,
install packages etc.

Over time, tools were developed which raised the level of abstraction away from
individual commands and towards configuration files. Now it is possible to write
text files which describe how a server should be configured and then use a
configuration management tool to make the relevant changes.

Despite the name, configuration management covers more than just service
configuration. It can be used to ensure software is installed, services are
started and running, deploy bespoke applications such as websites and more.

## Orchestration

You may also have heard of orchestration, which is often discussed alongside
configuration management. There is a degree of overlap between the two concepts,
but the broad difference is that configuration management relates to existing
machines (physical or virtual), whereas orchestration involves the creation of
machines (e.g. in responsed to demand). Due to time constraints, we won't be
covering orchestration in this workshop.

## Ansible

Ansible is one of several options available for configuration management. It is
open source and free software (GPLv3 licence) and you can follow the development
process on [GitHub](https://github.com/ansible). The company behind Ansible was
acquired by RedHat in October 2015, but this doesn't appear to have had an
impact on development, and if anything it is likely to make life easier for
system administrators already using RHEL, CentOS and OpenStack.

In case you were wondering, an ansible is a fictional machine for communicating
over large distances. It was first coined in *Rocannon's World* by Ursula K. Le
Guin, and is used by other science fiction novels.

## Alternatives

Although this workshop covers Ansible, it is worth spending a few minutes
looking at some of the popular alternatives. Examples which you may have heard
of include CfEngine, Puppet and Chef.

Puppet and Chef both have a strong Ruby background, and operate at a higher
level of abstraction than Ansible. With Puppet it's possible to say 'this
package must be installed' and Puppet will take the appropriate action on
different Linux distributions. With Ansible, you have to explicitly use the
`apt` module on Debian hosts, the `yum` module on RedHat etc. The major downside
is that Puppet and Chef require an agent to run on every managed node, which
complicates initial deployment.

There is no particularly strong technical reason to use Ansible over any of the
alternatives. Ansible probably has the gentlest learning curve of all the major
configuration management systems, and the fewest dependencies, but the final
choice is largely a personal or business preference.

## Terminology

Ansible divides machines into two categories:

*Control machine:* The machine which the Ansible client runs on. For the
purposes of the workshop, this will be your laptop.

*Managed node:* The machine which will have its configuration managed by
Ansible. In the workshop, this will be a virtual machine running on your laptop.
Sometimes nodes are referred to as *hosts*.

Ansible can manage Linux, OS X and Windows nodes, thought to keep things simple
we'll focus exclusively on Linux. We will also assume that the managed nodes are
servers, although the majority of material applies to desktops as well (e.g. if
you wanted to manage a cluster of machines in a teaching environment).

### Push deployment

Ansible uses *push deployment*, which means that changes are only deployed when
you run the relevant command on the control machine (usually this will be
`ansible-playbook`). The alternative mechanism, used by Puppet and Chef, is
*pull deployment*, whereby nodes regularly check for configuration updates and
apply them. There are advantages and disadvantages to both approaches, but
push deployment is generally easier to get up and running.

## Ansible requirements

Ansible has a small set of requirements for both the controlm machine and the
managed node.

**Control machine:** Python 2.5 or later.

**Managed nodes:** Python 2.5 or later, SSH service.

There is no requirement for the operating system on the control machine to match
the nodes, so you can control Linux nodes from a macOS machine, Windows nodes
from a Linux machine etc. However, running Ansible on Windows (as a control
machine or a managed node) is significantly more work than using Linux or macOS.

No agent software needs to run on the nodes and there are no additional firewall
rules required beyond allowing incoming SSH connections (you can restrict this
by IP address, username etc. if required).

For a single node, the easiest solution is usually to login straight after
installation to enable SSH and install Python. If you are planning to manage
more than a couple of nodes, it would make sense to create a base image which
includes SSH and Python, although some Linux distributions come with both by
default (e.g. Ubuntu Server).

## Number of nodes

Ansible can manage anything from a single node up to thousands of nodes. For
simplicity though we will still to a single node for most of the examples in
this workshop.

## Ansible components

Ansible is split into several components:

 * Configuration file
 * Inventory file
 * Playbooks
 * Modules

As a convention, we place all Ansible files in a subdirectory named `ansible`.
All other files used by Ansible (e.g. Apache configuration files) are stored in
`ansible/files`.

All Ansible files are plain text so you can use whichever editor you prefer. You
can (and should!) also put the files into version control to keep a history of
changes made.

### Configuration file

**Filename:** `ansible.cfg`

The configuration file allows us to specify defaults which apply to all managed
nodes. This file is structured in the same way as `.ini` files, which consist
of groups of key-value pairs separated by the equals symbol (`=`).

Example:

```ini
[defaults]
hostfile = hosts
host_key_checking = False
```

### Inventory file

**Filename:** `hosts`

The inventory file lists all the managed nodes. To keep things simple, we will
be using a single group which contains one node.

Example:

```
[testing]
vagrant ansible_host=127.0.0.1 ansible_port=2222 ansible_user=vagrant
ansible_private_key_file=../.vagrant/machines/default/virtualbox/private_key
```

### Playbooks

**Filename:** `*.yml`

Playbooks are the core of Ansible. They are written in YAML (Yet Another Markup
Language) and describe a series of tasks to be performend on a node.

### Modules

Modules are abstractions of functionality which you can use in your playbooks.
For example, there is an `apt` module, which makes it easy to install software
on systems which use APT for package management, such as Debian and Ubuntu.

Ansible ships with two collections of modules: *Core* and *Extras*. Core modules
will always be included with Ansible, whereas those in Extras may be separated
in future releases. Popular modules in Extras may be promoted to Core over time.
There is also an active community of developers who have written their own
*Third Party* modules, and of course you can do the same (though doing so is
beyond the scope of this workshop).

Other than whether they are provided by default and how they are installed,
you will use Core, Extras and Third Party modules in the same way. We will only
be using Core and Extras modules in this workshop.

## Our first server

Our first server will be based on an Ubuntu image, on top of which we will
install a firewall and configure it to allow all outgoing traffic and block all
incoming traffic (except SSH).

First of all, let's clone the Git repository which includes all of the workshop
exercises:

```
git clone https://github.com/pwaring/configuration-management-ansible.git
```

Then change into the `workshop/ex01` directory:

```
cd configuration-management-ansible/workshop/ex01
```

This directory contains an `ansible` directory and a file called `Vagrantfile`
with the following contents:

```ruby
Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
end
```

This tells Vagrant to configure a virtual machine with the following options:

 * `config.vm.box`: The default image to use for the virtual machine, in this
 case a 64 bit Ubuntu Trusty 14.04 image which is maintained by the community.

Start the virtual machine now by running:

```
vagrant up
```

Whilst the VM is starting up, we will create the three basic files required to
managed the node and install a basic firewall.

### Configuration file

The most basic configuration file tells Ansible where to find the inventory
file, which we'll discuss shortly. By convention, the inventory file is called
`hosts`. We also want to put all of our options into the `defaults` group, which
we can do by using the `[group]` syntax.

Create a file in the `ex01/ansible` directory called `ansible.cfg` and add the
following content:

```ini
[defaults]
inventory = hosts
```

Ansible's default behaviour is to check the host key each time it connects to a
node. In nearly all cases this is the correct behaviour, but because we will be
creating and destroying virtual machines several times in the workshop, we need
to disable this functionality by adding one more line to `ansible.cfg`:

```ini
host_key_checking = False
```

Only use this option for testing, and **never** in production.

Your `ansible.cfg` should now contain the following:

```ini
[defaults]
inventory = hosts
host_key_checking = False
```

Although there are dozens of options which can be specified in the configuration
file, these are the only two which we will be using in the workshop.
