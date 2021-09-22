Ansible has a small set of requirements for both the control machine and the
managed node.

**Control machine:** macOS or Linux, Python 3.

**Managed nodes:** Python >=2.7, >=3.5; SSH service.

There is no requirement for the operating system on the control machine to match
the nodes, so you can control Linux nodes from a macOS machine, Windows nodes
from a Linux machine etc. However, running Ansible on Windows as a control
machine is not officially supported.

No agent software needs to run on the nodes and there are no additional firewall
rules required beyond allowing incoming SSH connections (you can restrict this
by IP address, username etc. if required).

For a single node, the easiest solution is usually to login straight after
installation to enable SSH and install Python, or use Ansible's `raw` module to
issue the relevant installation command. If you are planning to manage more than
a couple of nodes, it would make sense to create a base image which includes SSH
and Python, although some Linux distributions come with both by default (e.g.
Ubuntu Server).
