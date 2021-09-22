---
title: Configuration Management with Ansible
author: Paul Waring
geometry: margin=2cm,vmargin=2.5cm
papersize: a4
fontfamily: palatino
fontsize: 11pt
---

## Workshop requirements

This document contains important information about the workshop, including
minimum hardware requirements and software which must be installed prior to
arrival. Even if you are confident that you have all the requirements,
please try running the sample exercise using the instructions below as this
will ensure that you have a copy of the virtual machine image that we will
be using during the workshop.

If you have any technical questions about the workshop, or there are any
particular areas which you would like us to spend more time on, please let the
tutor know via email: [paul@phpdeveloper.org.uk](mailto:paul@phpdeveloper.org.uk).

## Knowledge

This workshop assumes that attendees are familiar with the following:

 * Using a command line interface on Linux or macOS.
 * Using virtualisation to run a guest operating system.

No prior knowledge of Ansible or configuration management is required.

## Equipment

As this is a hands-on workshop, attendees will need to meet certain hardware
and software requirements.

You can test whether your machine meets the requirements by running the
following commands:

```
wget https://github.com/pwaring/configuration-management-ansible/archive/master.zip
unzip master.zip
cd configuration-management-ansible/workshop/exercises/ex00
vagrant up
```

The last few lines of output should be (don't worry if you get a message about
compatibility mode '2.0'):

```
==> default: Running provisioner: ansible...
    default: Running ansible-playbook...

PLAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
ok: [default]

TASK [ping] ********************************************************************
ok: [default]

PLAY RECAP *********************************************************************
default                    : ok=2    changed=0    unreachable=0    failed=0  
```

Make sure you destroy the VM afterwards by running the following command in
the `ex00` directory (same location as `Vagrantfile`):

```
vagrant destroy -f
```

### Hardware

Attendees will need a laptop with a CPU that supports hardware virtualisation.
Most laptops purchased since 2012 should have this functionality, but if in
doubt please contact the tutor who can check and help you enable hardware
virtualisation, as it is sometimes disabled by default.

As we will be using virtual machines for most of the exercises, your laptop will
need at least 4GB of RAM, and ideally more. A solid-state drive (SSD) as the
main hard drive will also help virtual machines to boot as quickly as possible.

### Software

A recent version of a Linux distribution or macOS is required. The tutor will
be using Ubuntu 20.04 (Focal Fossa). Windows is **not** supported. This
includes the Windows Subsystem for Linux and running Linux in a virtual
machine with Windows as the host. Please do not bring a laptop running Windows
as you will not be able to complete the workshop.

The following software should be installed before the workshop. Package names
have been supplied for Ubuntu but are likely to be similar on other
distributions. If you will be using a distribution other than Ubuntu and would
like a list of packages, please contact the tutor.

Attendees with a macOS laptop can visit the websites listed for each piece
of software, where downloads are available. Some software is also available
through [Homebrew](https://brew.sh/).

All the required software, with the exception of VirtualBox, supports the
`--version` command line option to print the current version.

#### Ansible

  * **Minimum version:** 2.9
  * **Ubuntu package:** `ansible`
  * **Website:** [ansible.com](https://www.ansible.com/)

#### VirtualBox

  * **Minimum version:** 6.1
  * **Ubuntu package:** `virtualbox`
  * **Website:** [virtualbox.org](https://www.virtualbox.org/)

#### Vagrant

* **Minimum version:** 2.2
* **Ubuntu package:** `vagrant`
* **Website:** [vagrantup.org](https://www.vagrantup.com/)

#### Python

* **Minimum version:** 3.5
* **Ubuntu package:** `python`
* **Website:** [python.org](https://www.python.org/)
