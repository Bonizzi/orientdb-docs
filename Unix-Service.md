<!-- proofread 2015-11-26 SAM -->

# Install as Service on Unix/Linux

Following the installation guide above, whether you chose to download binaries or build from source, does not install OrientDB at a system-level.  There are a few additional steps you need to take in order to manage the database system as a service.

OrientDB ships with a script, which allows you to manage the database server as a system-level daemon.  You can find it in the `bin/` path of your installation directory, (that is, at `$ORIENTDB_HOME/bin/orientdb.sh`.  

The script supports three parameters: 
- `start`
- `stop`
- `status`

## Configuring the Script

In order to use the script on your system, you need to edit the file to define two variables: the path to the installation directory and the user you want to run the database server.

<pre>
$ <code class="lang-sh userinput">vi $ORIENTDB_HOME/bin/orientdb.sh</code>

#!/bin/sh
# OrientDB service script
#
# Copyright (c) Orient Technologies LTD (http://www.orientechnologies.com)

# chkconfig: 2345 20 80
# description: OrientDb init script
# processname: orientdb.sh

# You have to SET the OrientDB installation directory here
ORIENTDB_DIR="YOUR_ORIENTDB_INSTALLATION_PATH"
ORIENTDB_USER="USER_YOU_WANT_ORIENTDB_RUN_WITH"
</pre>

Edit the `ORIENTDB_DIR` variable to indicate the installation directory.  Edit the `ORIENTDB_USER` variable to indicate the user you want to run the database server, (for instance, `orientdb`).

## Installing the Script

Different operating systems and Linux distributions have different procedures when it comes to managing system daemons, as well as the procedure for starting and stopping them during boot up and shutdown.  Below are generic guides for init and systemd based unix systems as well Mac OS X.  For more information, check the documentation for your particular system.

### Installing for init

Many Unix-like operating systems such as FreeBSD, most older distributions of Linux as well as current releases of Debian, Ubuntu and their derivatives use variations on SysV-style init for these processes.  These are typically the systems that manage such processes using the `service` command.

To install OrientDB as a service on an init-based unix or Linux system, copy the modified `orientdb.sh` file from `$ORIENTDB_HOME/bin` into `/etc/init.d/`:

<pre>
# <code class="lang-sh userinput">cp $ORIENTDB_HOME/bin/orientdb.sh /etc/init.d/orientdb</code>
</pre>

Once this is done, you can start and stop OrientDB using the `service` command:

<pre>
# <code class="lang-sh userinput">service orientdb start</code>
Starting OrientDB server daemon...
</pre>


### Installing for systemd

Most newer releases of Linux, especially among the RPM-based distributions like Red Hat, Fedora and CentOS, as well as future releases of Debian and Ubuntu use systemd for these processes.  These are the systems that manage such processes using the `systemctl` command.

Installing OrientDB on a systemd-based Linux distribution requires that you write a service file set to use the `orientdb.sh` script in launching the database server.  Place this file in the systemd configuration directory, (for instance, `/etc/systemd/`:

<pre>
# <code class="lang-sh userinput">vi /etc/systemd/system/orientdb.service</code>

[Unit]
Description=OrientDB Server
After=network.target
After=syslog.target

[Install]
WantedBy=multi-user.target

[Service]
Type=forking
ExecStart=$ORIENTDB_HOME/bin/orientdb.sh start
ExecStop=$ORIENTDB_HOME/bin/orientdb.sh stop
ExecStatus=$ORIENTDB_HOME/bin/orientdb.sh status
</pre>

You may want to use the absolute path instead of the environmental variable `$ORIENTDB_HOME`.  Once this file is saved, you can start and stop the OrientDB server using the `systemctl` command:

<pre>
# <code class="lang-sh userinput">systemctl start orientdb.service</code>
</pre>

Additionally, with the `orientdb.service` file saved, you can set systemd to start the database server automatically during boot by issuing the `enable` command:

<pre>
# <code class="lang-sh userinput">systemctl enable orientdb.service</code>
Synchronizing state of orientdb.service with SysV init with /usr/lib/systemd/systemd-sysv-install...
Executing /usr/lib/systemd/systemd-sysv-install enable orientdb
Created symlink from /etc/systemd/system/multi-user.target.wants/orientdb.service to /etc/systemd/system/orientdb.service.
</pre>


### Installing for Mac OS X

For Mac OS X, create an alias to OrientDB system daemon script and the console.

```sh
$ alias orientdb-server=/path/to/$ORIENTDB_HOME/bin/orientdb.sh
$ alias orientdb-console=/path/to/$ORIENTDB_HOME/bin/console.sh
```

You can now start the OrientDB database server using the following command:

```sh
$ orientdb-server start
```

Once the database starts, it is accessible through the console script.

<pre>
$ <code class="lang-sh userinput">orientdb-console</code>

OrientDB console v.1.6 www.orientechnologies.com
Type 'HELP' to display all the commands supported.

orientdb>
</pre>


## Other resources

To learn more about how to install OrientDB on specific environment please follow the guide below:
- [Install on Linux Ubuntu](http://famvdploeg.com/blog/2013/01/setting-up-an-orientdb-server-on-ubuntu/)
- [Install on JBoss AS](http://team.ops4j.org/wiki/display/ORIENT/Installation+on+JBoss+AS)
- [Install on GlassFish](http://team.ops4j.org/wiki/display/ORIENT/Installation+on+GlassFish)
- [Install on Ubuntu 12.04 VPS (DigitalOcean)](https://www.digitalocean.com/community/articles/how-to-install-and-use-orientdb-on-an-ubuntu-12-04-vps)
- [Install as service on Unix, Linux and MacOSX](Unix-Service.md)
- [Install as service on Windows](Windows-Service.md)
