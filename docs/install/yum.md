# Installing Percona Server for MongoDB on Red Hat Enterprise Linux and CentOS

This document describes how to install Percona Server for MongoDB on RPM-based distributions such as Red Hat Enterprise Linux and compatible derivatives.

!!! note

    Percona Server for MongoDB should work on other RPM-based distributions (for example, Amazon Linux AMI and Oracle Linux), but it is tested only on platforms listed on the [Percona Software and Platform Lifecycle](https://www.percona.com/services/policies/percona-software-platform-lifecycle#mongodb) page. 

## Package Contents

| Package                 | Contains                                 |
| ----------------------- | -----------------------------------------|
| `percona-server-mongodb`| The `mongo` shell, import/export tools, other client
utilities, server software, default configuration, and `init.d` scripts. |
| `percona-server-mongodb-server`| The mongod` server, default configuration files, and `init.d` scripts|
| `percona-server-mongodb-shell` | The `mongo` shell |
| `percona-server-mongodb-mongos`| The `mongos` sharded cluster query router |
| `percona-server-mongodb-tools` | Mongo tools for high-performance MongoDB fork from Percona|
| `percona-server-mongodb-dbg`   | Debug symbols for the server|

## Procedure

Percona provides the [`percona-release`](https://docs.percona.com/percona-software-repositories/index.html) configuration tool that simplifies operating repositories and enables to install and update both Percona Backup for MongoDB packages and required dependencies smoothly.

### Configure Percona repository


1. Install **percona-release**:

    ```{.bash data-prompt="$"}
    $ sudo yum install https://repo.percona.com/yum/percona-release-latest.noarch.rpm
    ```


2. Enable the repository: 
   
    ```{.bash data-prompt="$"}
    $ sudo percona-release enable psmdb-50 release
    ```

### Install Percona Server for MongoDB packages

=== "Install the latest version"

      To install the latest version of *Percona Server for MongoDB*, use the following command:

      ```{.bash data-prompt="$"}
      $ sudo yum install percona-server-mongodb
      ```

=== "Install a specific version"

     To install a specific version of *Percona Server for MongoDB*, do the following:

     1. List available versions:

         ```{.bash data-prompt="$"}
         $ sudo yum list percona-server-mongodb --showduplicates
         ```

        Sample output:

         ```
         Available Packages
         percona-server-mongodb.x86_64   5.0.2-1.el8   psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.3-2.el8   psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.4-3.el8   psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.5-4.el8   psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.6-5.el8   psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.7-6.el8   psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.8-7.el8   psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.9-8.el8   psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.10-9.el8  psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.11-10.el8 psmdb-50-release-x86_64
         percona-server-mongodb.x86_64   5.0.13-11.el8 psmdb-50-release-x86_64
         ```

     2. Install a specific version packages. For example, to install Percona Server for MongoDB 5.0.13-11, run the following command:

         ```{.bash data-prompt="$"}
         $ sudo yum install percona-server-mongodb-5.0.13-11.el8
         ```

## Run Percona Server for MongoDB

!!! note

    If you use SELinux in enforcing mode, you must customize your SELinux user policies to allow access to certain `/sys` and `/proc` files for OS-level statistics. Also, you must customize directory and port access policies if you are using non-default locations.

    Please refer to [Configure SELinux](https://docs.mongodb.com/v4.2/tutorial/install-mongodb-on-red-hat/#configure-selinux) section of MongoDB Documentation for policy configuration guidelines.

By default, Percona Server for MongoDB stores data files in `/var/lib/mongodb/`
and configuration parameters in `/etc/mongod.conf`.

**Starting the service**

Percona Server for MongoDB is not started automatically after installation.
Start it manually using the following command:

```{.bash data-prompt="$"}
$ sudo systemctl start mongod
```

**Confirming that service is running**

Check the service status using the following command: `service mongod status`

```{.bash data-prompt="$"}
$ sudo systemctl status mongod
```

**Stopping the service**

Stop the service using the following command: `service mongod stop`

```{.bash data-prompt="$"}
$ sudo systemctl stop mongod
```

**Restarting the service**

Restart the service using the following command: `service mongod restart`

```{.bash data-prompt="$"}
$ sudo systemctl restart mongod
```

### Run after reboot

The `mongod` service is not automatically started
after you reboot the system.

For RHEL or CentOS versions 5 and 6, you can use the `chkconfig` utility
to enable auto-start as follows:

```{.bash data-prompt="$"}
$ sudo chkconfig --add mongod
```

For RHEL or CentOS version 7, you can use the `systemctl` utility:

```{.bash data-prompt="$"}
$ sudo systemctl enable mongod
```