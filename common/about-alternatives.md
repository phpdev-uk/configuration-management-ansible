Although we will be focusing on Ansible, it is worth spending a few minutes
looking at some of the popular alternatives. Examples which you may have heard
of include CfEngine, Puppet, Chef and Salt.

Puppet and Chef both have a strong Ruby background and focus more on declaring
on how a server should look after configuration management has been applied,
rather than the steps used to achieve that state. Both systems also require you
to run a piece of software called an *agent* on every managed node, which
complicates initial deployment.

There is no particularly strong technical reason to use Ansible over any of the
alternatives. Ansible probably has the gentlest learning curve of all the major
configuration management systems, and the fewest dependencies, but the final
choice is largely a personal or business preference.
