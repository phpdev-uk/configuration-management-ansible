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
