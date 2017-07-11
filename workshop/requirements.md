---
title: Configuration Management with Ansible
author: Paul Waring
geometry: margin=2cm,vmargin=2.5cm
papersize: a4
fontfamily: palatino
---

## Workshop requirements

This document contains important information about the workshop, including
minimum hardware requirements and software which must be installed prior to
arrival.

You will also receive a copy of the notes by email two weeks before the
workshop, and a printed copy on the day of the workshop.

If you have any technical questions about the workshop, or there are any
particular areas which you would like us to spend more time on, please let the
tutor know via email: [paul@xk7.net](mailto:paul@xk7.net).

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
git clone https://github.com/pwaring/configuration-management-ansible.git
cd configuration-management-ansible/workshop/exercises/ex00
vagrant up
cd ansible
ansible testing -m ping
```

You should see the following output:

```
vagrant | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
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
need at least 4GB of RAM, and ideally more.

### Software

A recent version of a Linux distribution or macOS is required. The tutor will
be using Ubuntu 17.04 (Zesty Zapus).

The following software should be installed before the workshop. Package names
have been supplied for Ubuntu but are likely to be similar on other
distributions. If you will be using a distribution other than Ubuntu and would
like a list of packages, please contact the tutor.

Attendees with a macOS laptop can visit the websites listed for each piece
of software, where downloads are available. Some software is also available
through [Homebrew](http://brew.sh/).

#### Ansible

  * **Minimum version:** 2.0
  * **Ubuntu package:** `ansible`
  * **Website:** [ansible.com](http://www.ansible.com/)

#### Git

  * **Minimum version:** 1.8
  * **Ubuntu package:** `git`
  * **Website:** [git-scm.com](http://git-scm.com/)

#### VirtualBox

  * **Minimum version:** 4.3
  * **Ubuntu package:** `virtualbox`
  * **Website:** [virtualbox.org](https://www.virtualbox.org/)

#### Vagrant

* **Minimum version:** 1.7
* **Ubuntu package:** `vagrant`
* **Website:** [vagrantup.org](https://www.vagrantup.com/)

#### Python

macOS and most Linux distributions come with a recent version of Python 2
installed by default.

Ansible only supports Python 2.
