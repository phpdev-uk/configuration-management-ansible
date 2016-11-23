Although we will be focusing on Ansible, it is worth spending a few minutes
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
