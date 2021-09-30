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

At this time, Gaia Platform is in private preview. To request access, visit the [developer page](https://www.gaiaplatform.io/developers) on the main Gaia Platform website and complete the request form at the bottom of the page.

The information contained in this document represents information about prerelease features of the product. Features might change when the product is released for general availability.

---

This document provides guidance on setting up the Gaia SDK which includes the Gaia database server.

## Prerequisites

Before you begin, make sure that you have the following prerequisites:

-   Ubuntu Linux 20.04
-   [CMake](https://cmake.org/) build tools.
-   The [clang](http://clang.org/) compiler version 8 or higher.
-   A machine that supports the x64 architecture.

If you don't currently have cmake and clang installed, you can use the following commands to install them:

**sudo apt update && sudo apt upgrade && sudo apt install cmake clang**

To build Gaia samples using CMake and make tools, specify the clang compiler by setting the following variables in your environment:

```
export CC=/usr/bin/clang-10
export CPP=/usr/bin/clang-cpp-10
export CXX=/usr/bin/clang++-10
export LDFLAGS="-B/usr/lib/llvm-10/bin/ -fuse-ld=lld"
```

## Download the Gaia SDK

The Gaia SDK is delivered as a Debian software package (DEB).

**gaia-x.y.z__amd64.deb**

Where x.y.z represents the Gaia version number.

The Gaia SDK includes the Gaia database server executable and the Gaia Declarative C++ SDK.

To download the package, use the time-limited URL that was sent to you in your welcome email.

## Install the package

You must have sudo privileges to install the package.

To install the package:

1.  Navigate to the folder that contains the downloaded package.
2.  At the command prompt, run the following command:

**sudo apt install ./gaia-0.1.0_amd64.deb**

To remove the package:

1.  At the command prompt, run the following command:

    **sudo apt remove gaia**

To update the package, remove it and install the updated package:

1.  Download the updated package.
2.  Navigate to the folder that contains the downloaded package.
3.  To remove the currently installed package, run the following
    command:

    **sudo apt remove gaia**

1.  To install the new version, run the following command after replacing the x.y.z with the version number of the server that you are installing:

    **sudo apt install ./gaia-x.y.x_amd64.deb**

### Installed components

<pre>
/opt/gaia/bin
    gaia_db_server - The Gaia database catalog server.
    gaiac - Gaia Catalog compiler.
    gaiat - Gaia Translation Engine.
/opt/gaia/etc
    gaia.conf - Contains configuration settings for the platform and application loggers that the Gaia Platform uses.
    Gaia_log.conf - Configuration settings for the database and rules  engine that comprise the Gaia Platform.
/opt/gaia/examples/incubator
    Incubator example
/opt/gaia/include
    Include files for the Gaia Platform.
/opt/gaia/lib
    Library files for the Gaia Platform.
</pre>

## Start the Gaia server

The Gaia server must be running to build or run any solution that is based on the Gaia Platform.

We recommend that you don't run gaia\_db\_server under the root user, As with any daemon process that is accessible to the outside, running the Gaia server process as root, or any other account with special access rights, is a security risk. As best practice in production, run Gaia under a separate user account. This user account should only own the data that is managed by the server, and should not be used to run other daemons. For example, using the user nobody is not recommended.

To prevent a compromised server process from modifying the Gaia executables, the user account must not own the Gaia executable files.

Gaia server command line arguments:

|   |   |
|---|---|
|--data-dir \<database-folder-path>   | Specifies the location in which to store the Gaia database.  |
|--configuration-file-path \<config-file-name>  | Specifies the location in which to store the Gaia database.  |
| --disable-persistence  | No previous changes to the database will be visible after the database is started, and no changes made while the database is running will be visible after it is restarted.  | 
| --disable-persistence-after-recovery  | Previous changes to the database will be visible after the database is started, but no changes made while the database is running will be visible after it is restarted.  | 
|--reinitialize-persistent-store   |   All previous changes to the database will be deleted from persistent storage and will not be visible after the database is started, but changes made while the database is running will be visible after the database is restarted.  | 

To start the server on a machine that supports systemd:

**sudo systemctl start gaia**

### Starting the Gaia server on WSL

When starting the Gaia server on WSL, use the --data-dir argument to specify the location in which to store the database. We recommend that you store it locally in ~/.local/gaia/db.

To start the server on Ubuntu and run it in the background on WSL2 (Gaia has not been tested on WSL1):

**gaia_db_server --data-dir ~/.local/gaia/db &**

## Build and run the incubator example

You can verify that the Gaia SDK is installed properly by building and running the incubator demo. The source code for the incubator example is located in the /opt/gaia/examples/incubator folder.  For a full explanation of this this samples, see [Gaia incubator example](tutorials/gaia-incubator-example.md).

**Note**: If you encounter issues building and running the demo, the incubator folder contains a file named README.md. This document lists some of the common issues that you can encounter and suggested solutions.

Before you can build the incubator example, you must run cmake to build the makefiles for the example. To run cmake against the supplied CMakeLists.txt file that is installed with the Package:

1.  Copy the incubator demo into your folder:

    **cp -r /opt/gaia/examples/incubator .**

1.  Navigate to the incubator demo folder:

    **cd incubator**

To build and run the incubator example:

1.  Complete setup of the build environment by issuing the following
    commands:

       **mkdir build**<br>
       **cd build**<br>
       **cmake ..**<br>
       **make**

2.  When the build finishes, use the following command to run the
    example:

    **./incubator sim**

Use the menu to start, stop, show, and access the other functions of the simulation.

To monitor the incubator simulation, open another terminal window. Navigate to the build folder and run the following command:

**watch -n 1 ./incubator show**

This command displays the state of the incubators as well as the state of their associated sensors and fans. The status updates every second.
## Next Steps

* [Write your first Gaia App](tutorials/writing-first-gaia-application.md)
* Learn more about [Gaia Rulesets](rulesets-gaia-rulesets.md)
* [Building apps with Gaia](apps-building-gaia-apps.md)
