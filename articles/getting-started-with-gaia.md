# Getting Started with the Gaia Platform

---

**NOTE**

The information contained in this document represents information about prerelease features of the product. Features might change when the product is released for general availability.

---

This document provides guidance on setting up the Gaia SDK which includes the Gaia database server.

## Prerequisites

Before you begin, make sure that you have the following prerequisites
installed on your machine:

-   Ubuntu Linux 18.04 or 20.04
-   [Cmake](https://cmake.org/) build tools.
-   The [clang](http://clang.org/) compiler version 8 or higher.
-   A machine that supports the x86-64 architecture.

If you don’t currently have cmake and clang installed, you can use the following commands to install them:

**sudo apt update && apt upgrade && apt install cmake clang1**

## Download the Gaia package

The Gaia SDK is delivered as a Debian software package (DEB).

**gaia-0.1.0\_amd64.deb**

To download the package, use the time-limited URL that was sent to you in your welcome email.

## Install the package

You must have sudo privileges to install the package.

To install the package:

1.  Navigate to the folder that contains the downloaded package.
2.  At the command prompt, run the following command:

**sudo apt install ./gaia-0.1.0\_amd64.deb**

To remove the package:

1.  At the command prompt, run the following command:

    **sudo apt remove gaia**

To update the package, remove it and install the updated package:

1.  Download the updated package.
2.  Navigate to the folder that contains the downloaded package.
3.  Remove the currently installed package by running the following
    command:

    **sudo apt remove gaia**

1.  At the command prompt, run the following command:

    **sudo apt install ./gaia-&lt;x.y.x&gt;\_amd64.deb**

### Installed components

<pre>
/opt/gaia/bin
    gaia\_db\_server - The Gaia database catalog server.
    gaiac - Gaia Catalog compiler.
    gaiat - Gaia Translation Engine.
/opt/gaia/etc
    gaia.conf - Contains configuration settings for the platform and
    application loggers that the Gaia Platform uses.
    Gaia\_log.conf - Configuration settings for the database and rules
    engine that comprise the Gaia Platform.
/opt/gaia/examples/incubator
    Incubator example
/opt/gaia/include
    Include files for the Gaia Platform.
/opt/gaia/lib
    Library files for the Gaia Platform.
</pre>

## Start the Gaia server

The Gaia server must be running to build or run any solution that is
based on the Gaia Platform.

We recommend that you don’t run gaia\_db\_server under the root user, As
with any daemon process that is accessible to the outside, running the
Gaia server process as root, or any other account with special access
rights, is a security risk. As best practice in production, run Gaia
under a separate user account. This user account should only own the
data that is managed by the server, and should not be used to run other
daemons. For example, using the user nobody is not recommended.

To prevent a compromised server process from modifying the Gaia
executables, the user account must not own the Gaia executable files.

To start the server on a machine that supports systemd:

**sudo systemctl start gaia**

To start the server on Ubuntu running on WSL2 (not tested on WSL1):

**gaia\_db\_server &**

## Build and run the incubator example

You can verify that the Gaia SDK is installed properly by building and
running the incubator demo. The source code for the incubator example is
located in the /opt/gaia/examples/incubator\_demo folder.

**Note**: If you encounter issues building and running the demo, the
incubator\_demo folder contains a file named README.md. This document
lists some of the common issues that you can encounter and suggested
solutions.

Before you can build the incubator example, you must run cmake to build
the makefiles for the example. To run cmake against the supplied
CMakeLists.txt file that is installed with the Package:

1.  Copy the incubator demo into your folder:

    **cp -r /opt/gaia/examples/incubator .**

1.  Navigate to the incubator demo folder:

    **cd incubator**

To build and run the incubator example:

1.  Complete setup of the build environment by issuing the following
    commands:
    **mkdir build
    cd build
    cmake ..
    make**

2.  When the build finishes, use the following command to run the
    example:

**./incubator sim**

Use the menu to start, stop, show, and access the other functions of the
simulation.

To monitor the incubator simulation, open another terminal window and
run the following command:

**watch -n 1 ./incubator show**

This command displays the state of the incubators as well as the state
of their associated sensors and fans. The status updates every second.

## Building a Gaia Platform solution

After your design phase, the Gaia Platform architecture lends itself
best to the following workflow:

-   Create a schema for your application.
    -   The schema defines tables and fields that contain the state of
        your system at any given time.
    -   Add tables to manage the state of your application.
    -   Determine which columns in the schema drive the behavior. Your
        rules will act on these fields.

-   In your makefile run gaiac to import the schema and create tables
    for your database.

    -   Gaiac generates a header that contains the edc classes that
        define your schema in code. You include in this header your
        ruleset definition file.

-   Create your ruleset.

    -   Identify the actions to be performed when an Active Field
        changes.

-   Run gaiat to translate the ruleset into code files that you include
    in your app.

-   Create your application and call the code supplied by gaiat.

This is, of course, an iterative process. As you refine your
application, you will make changes to the schema and ruleset. A good
start is to add the input table with an Active Field, then add a ruleset
that contains a rule that fires when rows are inserted. Build from there
(iterate) with tables for output and managing the application’s state.

You define the schema in a DDL file.

```
create table if not exists names
(
    name string active
);

create table if not exists greetings
(
    greeting string active
);
```

When you run gaiac, the composer creates the tables in the catalog and
outputs header files that you include in your application.

When designing your solution for the Gaia Platform, keep in mind that
each rule runs within a separate OS thread, and each thread can only
have one outstanding transaction at a time. This provides transaction
isolation which means, two simultaneously running rules (each with a
unique thread and transaction) will never see each other's changes. A
rule must complete executing, which automatically commits its
transaction before the actions resulting from the rule can be seen by
the rest of the system.

To state this another way:

-   Rules run on separate threads. When data identified by an Active
    Field changes, it is possible for your app to check the database
    before rules based on the field run.
-   Rules processing is atomic, a rule must complete before you can see
    the results of actions due to the changes.

-   Rules only fire after the transaction that contains the change to
    the [Active Field](https://docs.google.com/document/d/17-cseJGZAL43d3YwBaLBF22DoiiwITCt_Ww0BaAyIeE/edit#heading=h.b7r1rpcs1grl)
    is committed. Said more succinctly, rules run post-commit.