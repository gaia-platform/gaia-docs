---
author: 
owner: 
lastupdate: 
---

# Writing your first Gaia application

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

In this walkthrough, you'll write and run your first Gaia Platform application. The code that you will write is also available under the /opt/gaia/examples/hello folder of the distribution package. If you encounter any errors along the way, you can compare the files that you generated from these instructions against those already provided.

The example walks you through most aspects of the Gaia Platform system.You'll learn how to:

-   Define a database schema and compile it with the Gaia Catalog Tool (gaiac).
-   Write a few simple rules and translate them using the Gaia Translation Engine (gaiat).
-   Write, build, and execute a simple application to fire our rules by inserting data into the database.

# Prerequisites

For information about the Gaia Platform prerequisites and installing the SDK, see [Getting Started with the Gaia Platform](../getting-started-with-gaia.md).

This walkthrough assumes that you are using Clang 10. 
# The Hello application

The goal of the application is to generate greetings for input names. To demonstrate the features of the system, the code inserts names into a table, which fires a Rule that generates greetings for those names and inserts them into a second table. The insertions into the second table fire a second Rule that prints the greetings to the console.

For this purpose, there are two tables:

-   A **names** table with a single **name** column, of string type.
-   A **greetings** table with a single **greeting** column, of string type.

There are also two rules:

-   A Rule that fires on insertions into the **names** table and that will in turn form and insert a greeting into the **greetings** table.
-   A Rule that fires on insertions into the **greetings** table and prints the greeting values to the console.

To put all of this together, you'll also write a small application that inserts some names into the **names** table to fire the rules.

# Creating a new application folder

Create a new folder in which to store the files for the application.

```
mkdir hello_sample
cd hello_sample
```

You'll execute all of the commands specified in this document in this folder.

# Specifying the Hello database schema

If you are familiar with SQL syntax the Gaia DDL definition format will be familiar to you.

In your source folder, create a hello.ddl text file. Copy and paste the following definitions in it:

```sql
create database if not exists hello;

use hello;

create table if not exists names
(
    name string
);

create table if not exists greetings
(
    greeting string
);
```

The `create database` and `use` statements are optional components of the DDL. If you do not include them, the gaia ddl compiler operates against the default database compiling the DDL.

The statements define the two tables, **names** and **greetings***, that that application uses. Each table has one database column.

You will refer to the database column names in the body of Declarative Rule expressions that you will define later. When your declarative code refers to one of these Fields with a read operation, Gaia fires an Event that schedules the associated Declarative Rules for execution.

The next step is to compile the definitions and generate the tables in the Gaia database. To do this, you use the gaiac tool. At the command line, in the folder in which you created the hello.ddl file, run the following command:

```gaiac hello.ddl -g --db-name hello -o hello```

The command instructs gaiac to process the hello.ddl file which creates the database and tables. The -g option tells gaiac to generate the Direct Access header and source files. The --db-name specifies which database to use and the -o option specifies the output folder for the generated files.

Gaiac prints a message that tells you where it wrote the gaia_hello.h file. This file contains definitions necessary to programmatically interact with the tables that are generated based on the definitions. You will see these referred to as Direct Access code.

Gaiac generates a second file name hello_generated.h. This is included by the gaia_hello.h file; you will not reference this file directly.

To verify that the tables were successfully created, run gaiac in an interactive mode:

```gaiac -i```

At the prompt, type the following command to list all the database tables.

**\lt**

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
    on_insert(names)
    {
        // Form the greeting using the name.
        string new_greeting = "Hello " + string(names.name) + "!";

        // Insert the greeting.
        gaia::hello::greetings_t::insert_row(new_greeting.c_str());
    }
    // Rule 2: Whenever a greeting is inserted,
    // output it to the console.
    on_insert(greetings)
    {
        // Output the greeting to the console.
        cout << endl << greetings.greeting << endl;
    }
}

```

The ruleset file defines two rules. The rules use the `on_insert()` attribute to watch for insertions to the names and greetings tables.

To insert the greeting into the greetings table, you use the **gaia::hello::greetings_t::insert_row** method. You can find the signature for the method in the *gaia_hello.h* file that gaiac generated.

Finally, the second rule outputs the greeting to the console.

The rules code looks very much like C++ but, before you can compile it, you must translate it into proper C++ code using the Gaia translator tool - gaiat.

To generate C++ code for these rules, execute the following command:

```gaiat hello.ruleset -output hello_ruleset.cpp -- -I /usr/lib/clang/10/include/ -I /opt/gaia/include/ -I hello ```

**NOTE**: The first two include paths of this command might need to be updated if Gaia and clang are installed in a non-standard way or if you're using a version of Clang other than 10.

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

Let's go over the main steps of this code:

-   **gaia::system::initialize()** initializes the Gaia system.
-   The insertion of the names needs to be done within a transaction that starts with **gaia::db::begin_transaction()** and completes with **gaia::db::commit_transaction()**. The actual insertions use the generated **gaia::hello::names_t** helper from gaia_hello.h.
-   The application completes its execution by calling **gaia::system::shutdown()**, which is the counterpart to the **gaia::system::initialize()** call.

To build this code, use the following command:

```clang++-10 hello.cpp hello_ruleset.cpp hello/gaia_hello.cpp /usr/local/lib/libgaia.so -I /opt/gaia/include -Wl,-rpath,/usr/local/lib -lpthread -o hello.run -I hello -stdlib=libc++```

If you are using a newer version of the clang compiler or if Gaia is installed in a non-standard location, update the command accordingly.

The output is a *hello,run* executable which represents your Hello application.

# Executing the Hello application

You are now ready to execute the app:

```./hello.run```

You should see output that looks similar to the following:

<pre>
Hello example is running...
Hello Bob!
Hello Alice!
Hello Charles!
Hello example has shut down.
</pre>

The order in which the rules are triggered is not deterministic. Due to this, the output of the program will vary from run to run.

## Next Steps

- Learn about [Gaia Rulesets](../rulesets-gaia-rulesets.md)
- Rum the [Gaia incubator example](../tutorials/gaia-incubator-example.md)