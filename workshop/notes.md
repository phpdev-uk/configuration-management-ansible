---
title: Configuration Management with Ansible
author: Paul Waring
geometry: margin=2cm,vmargin=2.5cm
papersize: a4
fontfamily: palatino
---

## Introduction

These notes outline the topics which will be covered in the workshop. They are
not a transcript of the workshop, but you should be able to use them as a
standalone resource.

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

## Configuration management

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
purposes of the workshop, the control machine will be your laptop.

*Managed node:* The machine which will have its configuration managed by
Ansible. In the workshop, the managed node will be a virtual machine running on
your laptop. Sometimes nodes are referred to as *hosts*.

Ansible can manage Linux, macOS and Windows nodes, though to keep things simple
we'll focus exclusively on Linux. We will also assume that the managed nodes are
servers, although the majority of material applies to desktops as well (e.g. if
you want to manage a cluster of machines in a teaching environment).

### Push deployment

Ansible uses *push deployment* by default, which means that changes are only
deployed when you run the relevant command on the control machine (usually this
will be `ansible-playbook`). The alternative mechanism, used by Puppet and Chef,
is *pull deployment*, whereby nodes regularly check for configuration updates
and apply them. There are advantages and disadvantages to both approaches, but
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
All other files used by Ansible (e.g. configuration files) are stored in
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
Core, Extras and Third Party modules are used in the same way. We will only
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
in place by using nmap to run a port scan against the virtual machine (from
your laptop):

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

```yaml
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

As well as the `apt` module, which we've already seen, we're introducing the
`service` module (Core) with the following options:

 * `name:` The name of the service.
 * `state:` The state of the service after running this task. `started` means
that the service must be running.

Run the playbook and then visit [http://10.213.213.213](http://10.213.213.213)
in a web browser. You should see a page which indicates that nginx is working.

Now run the `security.yml` playbook in `ex02/ansible` and try visiting the page
again. Your browser will not be able to connect, as the virtual machine is now
blocking all incoming connections other than SSH. To fix this, we need to add
two more tasks to the web playbook, immediately underneath and indented at the
same level as the existing tasks:

```yaml
- name: enable incoming http
  ufw:
    rule: allow
    to_port: http
- name: reload ufw
  ufw:
    state: reloaded
```

Run the `web.yml` playbook again and access to nginx should be restored.

## Single playbook

Although splitting out tasks into separate playbooks might seem sensible, as it
keeps different services separate, this is a bad idea for two reasons:

 1. It's easy to forget to run one playbook, or run them in the wrong order.
 1. Different services are likely to have dependencies on one another. For
 example, every service needs to add firewall rules - should these go in the
 service playbook or the security playbook?

From now on we will only use one playbook per managed node. Later we will
introduce the concept of *roles*, which allow you to create generic server
templates which can then be overridden on a per-server or per-group basis.

## Configuration files

Until now we've installed software and handled its configuration entirely
through playbooks. This is fine for simple changes, such as whether a package
is installed, a service is running etc. but it becomes messy if we want to
specify a large number of configuration options. Furthermore, it requires a
working knowledge of Ansible in order to configure a service, whereas in larger
teams you may have one person who is responsible for the server estate and
another who sets up individual pieces of software such as nginx. Not all
software has an Ansible module, and writing your own can be time-consuming.

What we really need at this point is the ability to copy existing configuration
files to a server, using Ansible. Fortunately, Ansible ships with a wide range
of Core modules for manipulating files.

Our test example will be adding a new virtual host to nginx. Enter the `ex03`
directory and run `vagrant up`.

Within the `ansible` directory, open `playbook.yml` and add this task below the
one which installs nginx:

```yaml
- name: create directory for new virtual host
  file:
    path: /var/www/html
    state: directory
    mode: 0755
    owner: root
    group: root
```

Let's go through the following line by line:

 * `file`: We are using the `file` module (Core).
 * `path`: Full path to the target.
 * `state`: The type of file to create. There are 6 options, but the two most
 common ones are `file` and `directory`.
 * `mode`: File permissions, either as octal (`0755`) or symbolic (`u=rwx,g=rx,o=rx`).
 * `owner`: Owner of the file/directory.
 * `group`: Group of the file/directory.

--------------------------------------------------------------------------------
**NOTE**

If the directory specified in `path` does not exist, Ansible will create it and
all parent directories. In our example both `/var/www` and `/var/www/html` will
be created, with the same permissions.
--------------------------------------------------------------------------------

```yaml
- name: copy index.html file
  copy:
    src: files/index.html
    dest: /var/www/html/index.html
    mode: 0644
    owner: root
    group: root
```

```yaml
- name: copy nginx-81 file
  copy:
    src: files/nginx-81
    dest: /etc/nginx/sites-available/nginx-81
    mode: 0644
    owner: root
    group: root

- name: symlink nginx-81
  file:
    state: link
    src: /etc/nginx/sites-available/nginx-81
    dest: /etc/nginx/sites-enabled/nginx-81

- name: reload nginx
  service:
    name: nginx
    state: reloaded
```

Open `http://10.213.213.213:81` in your browser and you should see a Hello World
page.

## Variables

So far we have set up a basic server but hardcoded all of our values (e.g. paths).
Fortunately Ansible allows us to declare variables and then use them throughout
the playbook.

Variables can be declared at the top of a playbook using the `vars` section:

```yaml
vars:
  website_path: /var/www/html
```

Variables can be used within a playbook using the `{{ var_name }}` syntax, e.g.

```yaml
copy:
  src: files/index.html
  dest: "{{ website_path }}/index.html"
```

We have already seen an example of lists in the `tasks` section. We can also
create list variables:

```yaml
vars:
  install_packages:
    - nginx
    - php-fpm
```
