The information contained in this document represents information about
prerelease features of the product. Features might change when the
product is released for general availability.

In this walkthrough, you’ll write and run your first Gaia Platform
application. The code that you will write is also available under the
/opt/gaia/examples/hello folder of the distribution package. If you
encounter any errors along the way, you can compare the files that you
generated from these instructions against those already provided.

The example walks you through most aspects of the Gaia Platform system.
You’ll learn how to:

-   Define a database schema and compile it with the Gaia Catalog Tool (gaiac).
-   Write a few simple rules and translate them using the Gaia Translation Engine (gaiat).
-   Write, build, and execute a simple application to trigger our rules by inserting data into the database.

# Prerequisites

For information about the Gaia Platform prerequisites and installing the
SDK, see [Getting Started with the Gaia Platform](../articles/getting-started-with-gaia.md).

# The Hello application

The goal of the application is to generate greetings for input names. To
demonstrate the features of the system, the code inserts names into a
table, which triggers a rule that generates greetings for those names
and inserts them into a second table. The insertions into the second
table trigger a second rule that prints the greetings to the console.

For this purpose, there are two tables:

-   A **names** table with a single **name** column, of string type.

-   A **greetings** table with a single **greeting** column, of string type.

There are also two rules:

-   A rule that triggers on insertions into the **names** table and that will in turn form and insert a greeting into the **greetings** table.

-   A rule that triggers on insertions into the **greetings** table and prints the greeting values to the console.

To put all of this together, you’ll also write a small application that inserts some names into the **names** table to trigger the rules.

# Creating a new application folder

Create a new folder in which to store the files for the application.

mkdir hello_sample

cd hello_sample

You’ll execute all of the commands specified in this document in this
folder.

# Specifying the Hello database schema

In your source folder, create a hello.ddl text file. Copy and paste the
following definitions in it:

```sql
create table if not exists names
(
    name string active
);

create table if not exists greetings
(
    greeting string active
);
```

If you are familiar with SQL syntax this definition format should be familiar. The statements define the two tables, **names** and **greetings***,* that our application uses. The keyword **active** is new and is specific to Gaia.

Active Fields are Database Column names that you refer to in the body of a Declarative Rule expression. When your declarative code refers to one
of these Fields with a read operation, Gaia triggers an Event that schedules the associated Declarative Rules execute.

The next step is to compile the definitions and generate the tables in the Gaia database. To do this you use the gaiac tool. At the command
line, in the folder in which you created the hello.ddl file, run the following command:

**gaiac -g hello.ddl**

gaiac prints a message that tells you where it wrote the gaia_hello.h file. This file contains definitions necessary to programmatically interact with the tables that are generated based on the definitions. You will see these referred to as Extended Data Classes, or EDC, for short.

Gaiac generates a second file name hello_generated.h. This is included by the gaia_hello.*h* file; you will not reference this file directly.

To verify that the tables were successfully created, run gaiac in an interactive mode:

**gaiac -i**

At the prompt, type the following command to list all the database tables.

**l**

You should see two rows for the **names** and **greetings** tables. The other entries are for system catalog tables or other tables that you might have generated with other examples. The entries that you are interested in are listed last and look similar to the following:


| Database  | Name  | ID  |Type|
|---|---|---|---|
| ...  | ...  | ...  | ...  |
|hello|names|48|1|
|hello|greetings|50|1|

Do not worry if the **ID** values or the **Type** values look different the important thing is to see the tables listed.

To exit the interactive gaiac session, use the following command:

**exit**

# Specifying the Hello rules

Create a new file and name it hello.ruleset. Copy and paste the following content to it:

```cpp
#include <iostream>
#include <string>

#include "gaia_hello.h"

using namespace std;

ruleset hello_ruleset
{
  // Rule 1: Whenever a name is inserted,
  // insert a new greeting into the greetings table.
  {
    // This LastOperation reference will indicate to the system
    // that this rule should be triggered
    // by any insertion made into the names table.
    if (names.LastOperation == INSERT)
    {
      // Form the greeting using the name.
      string new_greeting = "Hello " + string(names.name) + "!";

      // Insert the greeting.
      gaia::hello::greetings_t::insert_row(new_greeting.c_str());
    }
  }
  // Rule 2: Whenever a greeting is inserted,
  // output it to the console.
  {
    // This LastOperation reference will indicate to the system
    // that this rule should be triggered
    // by any insertion made into the greetings table.
    if (greetings.LastOperation == INSERT)
    {
       // Output the greeting to the console.
       cout << endl << greetings.greeting << endl;
    }
  }
}
```

The ruleset file defines two rules. Updates to the names table trigger the first rule. - this is caused by the rule simply referencing *names.LastOperation*. Because we’re only interested in acting on insert operations, the rule specifically checks that the last operation is an insert. INSERT is a system constant.

To insert the greeting that we constructed into the greetings table, we use the **gaia::hello::greetings_t::insert_row** method that was generated in the *gaia_hello.h* file by gaiac, when we compiled the table declarations.

Finally, the second rule outputs the greeting to the console.

The rules code looks very much like C++ but, before you can compile it, you must translate it into proper C++ code using the Gaia translator tool - gaiat.

To generate C++ code for these rules, execute the following command:

**gaiat hello.ruleset -output hello_rules.cpp -- -I /usr/lib/clang/8/include/ -I /opt/gaia/include/**

**NOTE**: The two include paths of this command might need to be updated if Gaia and clang have been installed in a non-standard way or if you’re using a version of Clang other than 8.

The output of this step is the hello_rules.cpp that contains the C++ version of our rules. You are now ready to compile these into an application.

# Writing the Hello application

Create a new file and name it hello.cpp. Copy and paste the following code into it:
```cpp
#include <iostream>

#include "gaia/system.hpp"
#include "gaia_hello.h"

using namespace std;

int main()
{
  cout
    << "Hello example is running..."
    << endl;

  gaia::system::initialize();

  gaia::db::begin_transaction();
  gaia::hello::names_t::insert_row("Alice");
  gaia::hello::names_t::insert_row("Bob");
  gaia::hello::names_t::insert_row("Charles");
  gaia::db::commit_transaction();

  gaia::system::shutdown();

  cout << "Hello example has shut down." << endl;
}

```

Let’s go over the main steps of this code:

-   **gaia::system::initialize()** initializes the Gaia system.
-   The insertion of the names needs to be done within a transaction     that we start with **gaia::db::begin_transaction()** and complete     with **gaia::db::commit_transaction()**. The actual insertions use the generated **gaia::hello::names_t** helper from     gaia_hello.h.
-   Our application completes its execution by calling gaia::system::shutdown(), which is the counterpart to our **gaia::system::initialize()** call.

To build this code, use the following command:

**clang++-8 hello.cpp hello_rules.cpp /usr/local/lib/libgaia.so -I /opt/gaia/include -Wl,-rpath,/usr/local/lib -o hello -lpthread**

If you are using a newer version of the clang compiler or if Gaia is installed in a non-standard location, update this command accordingly.
Its result should be a *hello* executable, which represents our Hello application.

# Executing the Hello application

You are now ready to execute the app:

**./hello**

You should see output that looks similar to the following:

Hello example is running...

Hello Bob!

Hello Alice!

Hello Charles!

Hello example has shut down.

The order in which the rules get triggered is not deterministic. This is why the output of the program will vary from run to run.
