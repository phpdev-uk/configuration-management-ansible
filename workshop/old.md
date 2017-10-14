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

--------------------------------------------------------------------------------
**NOTE**

In previous versions of Ansible, the above variables had `ssh` in their names,
e.g. `ansible_ssh_host`. This is deprecated as of Ansible 2.0 and the shorter
versions should be used.
--------------------------------------------------------------------------------

--------------------------------------------------------------------------------
**NOTE**

If you are running Debian Jessie, or any other distribution with a version of
Vagrant earlier than 1.7, you will need to set `ansible_private_key_file` to:

```
~/.vagrant.d/insecure_private_key
```

This is because before version 1.7, Vagrant used the same private key for all
virtual machines.
--------------------------------------------------------------------------------


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
