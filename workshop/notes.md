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

## Requirements

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
