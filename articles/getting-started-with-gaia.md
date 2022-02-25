---
author: Don Glover
owner: Don Glover
lastupdate: 
---

---
**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Getting Started with the Gaia Platform

---

**NOTE**

The information contained in this document represents information about prerelease features of the product. Features might change when the product is released for general availability.

---

This document provides guidance on setting up the Gaia SDK, which includes the Gaia Database Server.

## Prerequisites

Before you begin, make sure that you have the following prerequisites:

- Ubuntu Linux 20.04
- An environment that supports the x64 or the arm64 architecture. This can be:
  - A physical machine
  - A virtual machine
  - A subsystem such as Windows Subsystem for Linux
- The [clang](http://clang.org/) compiler version 8 or higher.
- [CMake](https://cmake.org/) is the officially supported method for building Gaia applications. In addition, the Gaia SDK uses CMake to automate code generation from Data Definition Language (DDL) and Ruleset files.

If you don't currently have cmake and clang installed, you can use the following commands to install them:

```bash
sudo apt update && sudo apt upgrade && sudo apt install cmake clang
```

To build Gaia examples using CMake and make tools, specify the Clang compiler by setting the following variables in your environment:

```bash
export CC=/usr/bin/clang-10
export CPP=/usr/bin/clang-cpp-10
export CXX=/usr/bin/clang++-10
export LDFLAGS="-B/usr/lib/llvm-10/bin/ -fuse-ld=lld"
```

## Download the Gaia SDK

The Gaia SDK is delivered as a Debian software package (DEB):

gaia-x.y.z_amd64.deb

Where x.y.z represents the Gaia version number.

The Gaia SDK includes the Database Server executable and the Gaia Declarative C++ SDK.

To download the package, use the URL that you were sent in your welcome email.

## Install the package

You must have sudo privileges to install the package.

To install the package:

1. Open a terminal session.
1. Navigate to the folder that contains the downloaded package.
1. At the command prompt, run the following commands; replacing the x.y.z with the correct version number:

    ```bash
    sudo apt-get update
    sudo apt-get install ./gaia-x.y.z_amd64.deb
    ```

During the installation process Gaia asks whether you want to install Gaia server as a systemd service. If you do not install the database as a service, you must run it from the command line prior running the Gaia tools or running a Gaia app.

**NOTE**: The database is configured as a systemd service. On platforms that do not support systemd the request to install as a service is not honored.

To reconfigure whether the database is installed as a service, run the following command:

```bash
sudo dpkg-reconfigure gaia
```

To remove the package:

1. At the command prompt, run the following command:

    ```bash
    sudo apt-get remove gaia
    ```

To update the package, remove it and install the updated package:

1. Download the updated package.
2. Remove the currently installed package, run the following command:

    ```bash
    sudo apt-get remove gaia
    ```

3. Navigate to the folder that contains the updated package.
4. After replacing the x.y.z with the version number of the package that you are installing, run the following command:

    ```bash
    sudo apt-get install ./gaia-x.y.z_amd64.deb
    ```

### Installed components

<pre>
/opt/gaia/bin
    gaia_db_server - The Gaia Database Server.
    gaiac - Gaia Catalog Tool.
    gaiat - Gaia Translation Engine.
/opt/gaia/etc
    gaia.conf - Contains configuration settings for the platform and application loggers that the Gaia Platform uses.
    gaia_log.conf - Contains configuration settings for the Database Server and rules engine that comprise the Gaia Platform.
/opt/gaia/examples/
    Example apps.
/opt/gaia/include
    Include files for the Gaia Platform.
/opt/gaia/lib
    Library files for the Gaia Platform.
</pre>

## Starting the Gaia server

To build or run any solution that is based on the Gaia Platform, the Gaia server must be running .

We recommend that you don't run gaia\_db\_server in production under the root user. As with any daemon process that is accessible to the outside, running the Database Server process as root, or any other account with special access rights, is a security risk. As best practice in production, run Gaia under a separate user account. This user account should only own the data that is managed by the server, and should not be used to run other daemons. For example, using the user `nobody` is not recommended.

To prevent a compromised server process from modifying the Gaia executables, in production the user account should not own the Gaia executable files.

When starting the Database Server, we recommend that you use the --data-dir argument to specify the location to store the database and that you create a separate database for each project. When the installer configures the database server to run as a server, it specifies the default database location.

To start the server from the command line:

```bash
gaia_db_server --data-dir .benchmark_db
```

During development and testing it can be useful to start with a clean database. To facilitate this, you can run the server with data persistence disable.

To start the server from the command line with persistence disabled:

```bash
gaia_db_server --persistence disable
```

Gaia server command line arguments:

| Option  | Description  |
|---|---|
| --persistence \<mode> | If not specified, the default mode is enabled.<br>The data location is specified with --data-dir.<br>- <enabled>: Persist data [default].<br>- <disabled>: Do not persist any data.<br>- <disabled-after-recovery>: Load data from the datastore and disable persistence.  | 
| --data-dir \<database-folder-path> | Specifies the location in which to store the database.  |
| --configuration-file-path \<config-file-name> | Specifies the location in which to store the Gaia configuration file.  |
| --reinitialize-persistent-store | All previous changes to the database are deleted from persistent storage and will not be visible after the Database Server is started, Changes to the database made while the Database Server is running will be visible after it is restarted.  | 

### Configuration settings

#### Overcommit policy

The Database Server can run normally with an overcommit policy value of 0 (heuristic overcommit.), but might become unstable under rare conditions.

To ensure stable performance under all conditions, we recommend changing the overcommit policy to 1 (always overcommit.)

To temporarily enable this policy, open a shell with root privileges and type the following command:

```bash
echo 1 > /proc/sys/vm/overcommit_memory
```

To permanently enable this policy:

1. Open /etc/sysctl.conf in an editor with root privileges and add the line

    vm.overcommit_memory=1

2. Save the file, and in a shell with root privileges type

    sysctl -p
  
#### Open file descriptor limit

The Database Server requires a per-process open file descriptor limit of at least 65535.

To temporarily set the minimum open file descriptor limit, open a shell with root privileges and type the following command:

```bash
  ulimit -n 65535
```

To permanently set the minimum open file descriptor limit:

1. Open /etc/security/limits.conf in an editor with root privileges and add the following lines:

     ```
    * soft nofile 65535
    * hard nofile 65535
    ```

    **Note**: For enhanced security, replace the wild card '*' in these file entries with the user name of the account that is running the  Database Server.

2. Save the file and start a new terminal session.

### Starting the Gaia Database Server on a machine that supports systemd

To start the server on a machine that supports systemd:

```bash
sudo systemctl start gaia
```

### Starting the Gaia Database Server on Windows Subsystem for Linux (WSL)

To start the server on WSL2 running Ubuntu and run it in the background (Gaia has not been tested on WSL1):

```bash
gaia_db_server --data-dir .<dbname> &
```

## Next Steps

- [Building apps with Gaia](apps-building-gaia-apps.md)
- [Write your first Gaia App](tutorials/writing-first-gaia-application.md)
- Learn more about [Gaia Rulesets](rulesets-gaia-rulesets.md)
