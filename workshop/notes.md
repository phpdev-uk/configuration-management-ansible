---
title: Configuration Management with Ansible
author: Paul Waring
geometry: margin=2cm,vmargin=2.5cm
papersize: a4
fontfamily: palatino
---

## Introduction

These notes outline the topics which will be covered in the workshop. They are
not a verbatim transcript of the workshop, but you should be able to use them
as a standalone resource.

If you are attending the workshop, you are welcome to read the notes beforehand
and send any questions or comments to the tutor at:
[paul@xk7.net](mailto:paul@xk7.net)

## The tutor

#include "../common/author-bio.md"

## Audience

This workshop is aimed at people who are familiar with administering Linux
systems but are new to either configuration management or Ansible.

## Workshop requirements

Please see the separate requirements document for full details of the software
and hardware requirements for this workshop.

## Configuration Management

#include "../common/about-configuration-management.md"

## Orchestration

You may also have heard of orchestration, which is often discussed alongside
configuration management. There is a degree of overlap between the two concepts,
but the broad difference is that configuration management relates to existing
machines (physical or virtual), whereas orchestration involves the creation of
machines (e.g. in response to demand). Due to time constraints, we won't be
covering orchestration in this workshop.

## Ansible

#include "../common/about-ansible.md"

## Alternatives

#include "../common/about-alternatives.md"

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
you want to manage a cluster of machines in a teaching environment).

### Push deployment

Ansible uses *push deployment*, which means that changes are only deployed when
you run the relevant command on the control machine (usually this will be
`ansible-playbook`). The alternative mechanism, used by Puppet and Chef, is
*pull deployment*, whereby nodes regularly check for configuration updates and
apply them. There are advantages and disadvantages to both approaches, but
push deployment is generally easier to get up and running.

## Ansible requirements

#include "../common/ansible-requirements.md"

## Number of nodes

Ansible can manage anything from a single node up to thousands of nodes. For
simplicity though we will stick to a single node for most of the examples in
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
can, and should, put the files into version control to keep a history of changes
made.

### Configuration file

**Filename:** `ansible.cfg`

The configuration file allows us to specify defaults which apply to all managed
nodes. This file is structured in the same way as `.ini` files, which consist
of groups of key-value pairs separated by the equals symbol (`=`).

Example:

```ini
[defaults]
inventory = hosts
host_key_checking = False
```

### Inventory file

**Filename:** `hosts`

The inventory file lists all the managed nodes. To keep things simple, we will
be using a single group which contains one node. This file uses a list of
key-value pairs separated by spaces.

Example:

```
[testing]
vagrant ansible_host=127.0.0.1 ansible_port=2222 ansible_user=vagrant
ansible_private_key_file=../.vagrant/machines/default/virtualbox/private_key
```

### Playbooks

**Filename:** `*.yml`

Playbooks are the core of Ansible. They are written in YAML (Yet Another Markup
Language) and describe a series of tasks to be performed on a node.

### Modules

Modules are abstractions of functionality which you can use in your playbooks.
For example, there is an `apt` module, which makes it easy to install software
on systems which use APT for package management, such as Debian and Ubuntu.

Ansible ships with two collections of modules: *Core* and *Extras*. Core modules
will always be included with Ansible, whereas those in Extras may be separated
in future releases. Popular modules in Extras may be promoted to Core over time.
There is also an active community of developers who have written their own
*Third Party* modules, and of course you can do the same -- though doing so is
beyond the scope of this workshop.

Other than whether they are provided by default and how they are installed,
you will use Core, Extras and Third Party modules in the same way. We will only
be using Core and Extras modules in this workshop.

## Our first server

Our first server will be based on an Ubuntu image, on top of which we will
install a firewall and configure it to allow all outgoing traffic and block all
incoming traffic (except SSH).

First of all, clone the Git repository which includes all of the workshop
exercises:

```
git clone https://github.com/pwaring/configuration-management-ansible.git
```

Then change into the `workshop/exercises/ex01` directory:

```
cd configuration-management-ansible/workshop/exercises/ex01
```

This directory contains an `ansible` directory and a file called `Vagrantfile`
with the following contents:

```ruby
#include "exercises/ex01/Vagrantfile"
```

This tells Vagrant to configure a virtual machine with the following options:

 * `config.vm.box`: The default image to use for the virtual machine, in this
 case a 64 bit Ubuntu Trusty 14.04 image which is maintained by the community.
 * `config.vm.network`: Create a private network for this virtual machine, with
 its own IP address.

Start the virtual machine now by running:

```
vagrant up
```

Whilst the VM is starting up, we will create the three basic files required to
managed the node and install a basic firewall.

--------------------------------------------------------------------------------
**NOTE**

You must run `vagrant up` from within the `exercises/ex01` directory. If not,
you will receive a warning message:

A Vagrant environment or target machine is required to run this
command. Run `vagrant init` to create a new Vagrant environment. Or,
get an ID of a target machine from `vagrant global-status` to run
this command on. A final option is to change to a directory with a
Vagrantfile and to try again.
--------------------------------------------------------------------------------

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

--------------------------------------------------------------------------------
**WARNING**

Only set `host_key_checking` to `False` for testing, **never** in production.
--------------------------------------------------------------------------------

Your `ansible.cfg` should now contain the following:

```ini
[defaults]
inventory = hosts
host_key_checking = False
```

Although there are dozens of options which can be specified in the configuration
file, these are the only two which we will be using in the workshop.

### Inventory file

Create a file in the `ex01/ansible` directory called `hosts` and add the
following content. Everything from `vagrant` onwards must be on one line, there
is only a break in the notes because of margin constraints.

```ini
vagrant ansible_host=10.213.213.213 ansible_user=vagrant
ansible_private_key_file=../.vagrant/machines/default/virtualbox/private_key
```

As with the configuration file, a name between two square brackets defines a
group, which contains one or more nodes. This allows you to manage a number of
nodes without having to refer to each one individually.

Each line under a group refers to a specific node. The first item is the name of
the node, in this case `vagrant` (there is no requirement for the name used by
Ansible to match the DNS hostname). After the name is a set of key-value pairs,
which must all be on the same line as the name.

Hopefully most of the names are self-explanatory, but here's a rundown of what
each one does:

 * `ansible_host`: The hostname or IP address of the node. In this case we're
 using the IP address which we specified in `Vagrantfile`.
 * `ansible_user`: The username for SSH connections. Vagrant defaults to a user
 called `vagrant` which has `sudo` access without the need for a password.
 * `ansible_private_key_file`: The path to the private key used for SSH
 connections. This defaults to `~/.ssh/id_rsa`, but we need to provide a
 specific value for Vagrant.

**Note:** If you are running Debian, or any other distribution with a version of
Vagrant earlier than 1.7, you will need to set `ansible_private_key_file` to:

```
~/.vagrant.d/insecure_private_key
```

This is because before version 1.7, Vagrant used the same private key for all
virtual machines.

Although there are many other options that you can specify in the inventory
file, these are the only ones we will be using in the workshop.

Test that the virtual machine is running and responsive by running the following
command from within the `ansible` directory:

```
ansible all ping -m
```

The following output should be returned:

```
vagrant | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

If you receive the following output:

```
ansible_private_key_file=../.vagrant/machines/default/virtualbox/private_key
    | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh.",
    "unreachable": true
}
vagrant | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh.",
    "unreachable": true
}
```

You have a line break somewhere in your definition of the `vagrant` host in your
inventory file.

The following output means you have run the command outside the `ansible`
directory:

```
[WARNING]: provided hosts list is empty, only localhost is available
```


### Firewall playbook

The first thing to do after creating a new server is to install a packet filter
or firewall. Create a file called `security.yml` in `ex01/ansible` with the
following content (the indentation is important):

```yaml
# Security-related functionality, e.g. firewall installation and setup
- name: Security playbook
  hosts: vagrant
  become: yes
  become_user: root
```

The first line is a comment, a concept which you may recognise from
programming languages and shell scripts. Anything after the `#` symbol to the
end of the line is ignored by Ansible, so you can add notes documenting parts
of the playbook. Comments can be at the end of a line or a full line by
themselves. There are no multi-line or block comments available in YAML.

The second line is a name for the playbook. This will be printed to the console
when you run the playbook, so it is a good idea to set this to a brief
description of what the playbook does.

The third line is the node on which the playbook will run. This can be set to an
individual node, as is the case here, or a group.

The fourth and fifth lines tells Ansible to 'become' the `root` user when
connecting to the node, as most of the tasks will require root
privileges. `become_user` does not imply `become: yes`, so you need to include
both.

--------------------------------------------------------------------------------
**NOTE**

You may come across older playbooks and tutorials which use `sudo` instead of
`become` and `become_user`. This syntax was deprecated in Ansible 1.9 and will
result in a warning being displayed when the playbook is run.
--------------------------------------------------------------------------------

By default, Vagrant images include a `vagrant` user which has `sudo` access
without needing a password. In production systems you can choose to connect as a
user with low privileges and then become root, or you can connect directly as
the root user. If a password is required for either SSH or sudo, Ansible will
prompt you for this information when you run the playbook.

To run a playbook, use the `ansible-playbook` command with the name of the
playbook as an argument. This must be run in the same directory as
`ansible.cfg` otherwise Ansible will not pick up the settings in that file:

```
ansible-playbook security.yml
```

You should see the following output:

```
PLAY [Security playbook] *******************************************************

TASK [setup] *******************************************************************
ok: [vagrant]

PLAY RECAP *********************************************************************
vagrant                    : ok=1    changed=0    unreachable=0    failed=0
```

Let's look at each line in turn:

 * `PLAY [Security playbook]`: Information about which playbook is being run,
 taken from the `name` we specified at the top of the file.
 * `TASK [setup]`: Ansible is collecting information about the node which it is
 about to configure. This includes information such as which packages are
 already installed on the node. If you are running an older version of Ansible,
 you may see `GATHERING FACTS` instead of `TASK [setup]`.
 * `PLAY RECAP`: Shows what has changed as a result of running this playbook,
 and whether any tasks failed to run.

The four values under `PLAY RECAP` are:

 * `ok=1`: The number of tasks completed successfully. In this case we have
 only executed one task, the pre-defined `setup`.
 * `changed=0`: The number of tasks which resulted in a change being made.
 Since the `setup` task makes no changes and we haven't defined any other tasks,
 this is zero.
 * `unreachable=0`: The number of nodes to which an SSH connection could not be
 established. This should always be zero.
 * `failed=0`: The number of tasks which failed. This should always be zero.

Now that we have a basic playbook, let's install the firewall software. We will
be using UFW, the Uncomplicated Firewall, which is an interface to the
`iptables` and `ip6tables` packet filtering.

Add the following lines to `security.yml`, underneath `become_user` and indented
at the same level:

```yaml
tasks:
  - name: Install UFW
    apt:
      name: ufw
      state: present
```

Let's look at each line in turn:

 * `tasks:` The following list items (indicated by a `-` in YAML) are tasks,
 the core of every playbook.
 * `name:` The name of this task. As with the playbook name, this is printed to
 the console when the task runs.
 * `apt:` The name of the module (`apt` is a Core module).
 * `name:` The name of the package.
 * `state:` The state of the package after running this task. `present` means
 that the package must be installed.

Now run the playbook:

```
ansible-playbook security.yml
```

You should see the following output:

```
PLAY [Security playbook] *******************************************************

TASK [setup] *******************************************************************
ok: [vagrant]

TASK [Install UFW] *************************************************************
ok: [vagrant]

PLAY RECAP *********************************************************************
vagrant                    : ok=2    changed=0    unreachable=0    failed=0
```

We've successfully run two tasks (`setup` and `Install UFW`) so Ansible now
shows `ok=2`. Why is `changed=0` though? In this case, the Ubuntu image we are
using already has the `ufw` package installed. Ansible has detected this as part
of the `setup` task and has determined that no action needs to be taken.

We can login to the virtual machine and verify that UFW is installed:

```
$ vagrant ssh
vagrant@vagrant-ubuntu-trusty-64:~$ sudo ufw status
Status: inactive
```

An inactive firewall with no filtering rules isn't very useful, so we'll add
some tasks to the playbook, indented underneath `tasks` at the same level as
the UFW installation lines:

```yaml
- name: start ufw
  ufw:
    state: enabled
- name: enable incoming ssh
  ufw:
    rule: allow
    to_port: ssh
- name: allow all outgoing traffic
  ufw:
    direction: outgoing
    policy: allow
- name: deny all incoming traffic
  ufw:
    direction: incoming
    policy: deny
    log: yes
- name: reload ufw
  ufw:
    state: reloaded
```

Each task uses the `ufw` module (Extras) to perform a configuration action.
Hopefully the YAML is self-documenting, but here is what each task does:

 1. Ensure that the UFW service has started (`enabled`).
 1. Allow incoming SSH traffic (if a service is running on the default port,
   you can specify it by name).
 1. Allow all outgoing traffic (default policy).
 1. Deny and log all incoming traffic (default policy).
 1. Reload UFW so that the rules take effect.

We add the permissive rules first, followed by the defaults. In firewalls the
order is usually important, because the first rule matched is used. However,
UFW permits incoming traffic if it matches an allow rule, regardless of where
it is specified. This will be useful in the next exercise when we come to add
a web server to our setup.

--------------------------------------------------------------------------------
**NOTE**

The UFW service will be reloaded even if Ansible has not made any
changes to the rules. It is possible to reload services only if something has
changed by creating a special type of task called a *handler*, and then
*notifying* the handler whenever something changes. It's easy to forget to do
this though, so for now we will always reload the service at the end of the
playbook.
--------------------------------------------------------------------------------

Run the playbook again and you should see the following output:

```
PLAY [Security playbook] *******************************************************

TASK [setup] *******************************************************************
ok: [vagrant]

TASK [Install UFW] *************************************************************
ok: [vagrant]

TASK [start ufw] ***************************************************************
changed: [vagrant]

TASK [enable incoming ssh] *****************************************************
changed: [vagrant]

TASK [allow all outgoing traffic] **********************************************
ok: [vagrant]

TASK [deny all incoming traffic] ***********************************************
ok: [vagrant]

TASK [reload ufw] **************************************************************
ok: [vagrant]

PLAY RECAP *********************************************************************
vagrant                    : ok=7    changed=2    unreachable=0    failed=0
```

We can see that seven tasks ran successfully (`ok=7`) and two resulted in
changes. Since the default policy of UFW is to allow all outgoing traffic and
deny all incoming traffic, these two tasks did not result in changes.

If you run the playbook again, you should see that seven tasks run successfully
but this time there are no changes.

Login to the virtual machine to verify that the rules have been setup correctly:

```
$ vagrant ssh
vagrant@vagrant-ubuntu-trusty-64:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
```

We can see that the UFW service is active and incoming SSH connections are
allowed from anywhere, over IPv4 and IPv6. We can verify that the firewall is
in place by using nmap to run a port scan against the virtual machine:

```
sudo nmap -sS 10.213.213.213
```

The output should be similar to:

```
Nmap scan report for 10.213.213.213
Host is up (0.00056s latency).
Not shown: 999 filtered ports
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 08:00:27:CC:95:5C (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 18.76 seconds
```

If we login to the virtual machine we can verify that UFW has blocked and logged
the incoming traffic on all ports other than SSH:

```
vagrant@vagrant-ubuntu-trusty-64:~$ sudo tail -n 2 /var/log/syslog
Nov 22 12:31:05 vagrant-ubuntu-trusty-64 kernel: [  156.774909] [UFW BLOCK]
IN=eth1 OUT= MAC=08:00:27:cc:95:5c:0a:00:27:00:00:05:08:00 SRC=192.0.2.1
DST=10.213.213.213 LEN=44 TOS=0x00 PREC=0x00 TTL=59 ID=9884 PROTO=TCP SPT=51556
DPT=554 WINDOW=1024 RES=0x00 SYN URGP=0
Nov 22 12:31:05 vagrant-ubuntu-trusty-64 kernel: [  156.774923] [UFW BLOCK]
IN=eth1 OUT= MAC=08:00:27:cc:95:5c:0a:00:27:00:00:05:08:00 SRC=192.0.2.1
DST=10.213.213.213 LEN=44 TOS=0x00 PREC=0x00 TTL=43 ID=63657 PROTO=TCP SPT=51556
DPT=199 WINDOW=1024 RES=0x00 SYN URGP=0
```

We've finished with this virtual machine now. Run the following command inside
the `ex01` directory to destroy the virtual machine and free up its resources:

```
vagrant destroy -f
```

## Adding a web server

A basic virtual machine with a firewall still doesn't do a great deal, so we
will expand on the previous exercise by adding a web server to our managed node.

There are two things we need for a basic web server:

 1. Ensure the web server (in this case nginx) is installed and running.
 1. Allow incoming traffic on the HTTP port (80).

Change into the `workshop/exercises/ex02` directory and run `vagrant up`.

Create a new playbook, `web.yml` in the `ex02/ansible` directory and add the
following content:

```
# Web server playbook
- name: Web server playbook
  hosts: vagrant
  become: yes
  become_user: root

  tasks:
    - name: install nginx
      apt:
        name: nginx
        state: present
    - name: ensure nginx is running
      service:
        name: nginx
        state: started
```

Run the playbook and then visit [http://10.213.213.213](http://10.213.213.213)
in a web browser. You should see a page which indicates that nginx is working.

Now run the `security.yml` playbook in `ex02/ansible` and try visiting the page
again. Your browser will not be able to connect, as the virtual machine is now
blocking all incoming connections other than SSH. To fix this, we need to add
two more tasks to the web playbook, immediately underneath and indented at the
same level as the existing tasks:

```
- name: enable incoming http
  ufw:
    rule: allow
    to_port: http
- name: reload ufw
  ufw:
    state: reloaded
```
