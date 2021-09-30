---
author: Don Glover
owner: Don Glover
lastupdate: 
---

# Gaiac

---
**NOTE**

The information contained in this document represents information about prerelease features of the product. Features might change when the product is released for general availability.

---

The Gaia Catalog Tool (gaiac) creates the datastore and tables that support your application. It also translates Gaia DDL files into the Gaia headers you include in your ruleset and app code files.

## Usage

Usage: gaiac [options] [ddl_file]

Command line arguments


| **Options**      |                       | **Description**       |
|-------------------|-----------------------|-----------------------|
| -d \<dbname\>     | \--db-name \<dbname\> | Specifies the database name to use when generating the Direct Acess source files.|
| -i                | --interactive         | Run gaiac in interactive mode. For more information on the available commands, see the Interactive mode commands section below. |
| -g                | \--generate           | Generates the Gaia headers and database tables for the specified DDL file.<br>If the -d argument does not specify the database name, gaiac uses the default database '()'.|
| -o \<path\>       | \--output \<path\>    | Sets the output directory for all generated files.|
| -h                | \--help               | Prints help information.          |
| -v                | \--version            | Prints version information.          |
| \<ddl file name\> |                       | Specifies the DDL file to process.   |

## Interactive mode commands

In the interactive mode, the following commands are available:


| **Command**                      | **Description**                  |
|-------------------|-----------------------|
| CREATE DATABASE [if not exists] DATABASE_NAME | Creates a database with the specified database name. If the command succeeds, gaiac returns to the prompt. If the command fails, gaiac returns an error message and returns to the prompt.  |
| CREATE [if not exists] [DATA_BASE NAME.]TABLE ( \<field definitions\>) | Creates a table with the specified name and fields. If the command succeeds, gaiac returns to the prompt. If the command fails, gaiac returns an error message and returns to the prompt.                                  <p>For more information on table specifications, see the CREATETABLE section below. </p>|
| DROP DATABASE NAME;               | Removes the database specified  by NAME from the catalog.        |
| DROP TABLE [DATA_BASE_NAME.]TABLE_NAME; | Removes the table specified by TABLE_NAME from the specified database specified by TABLE_NAME. If you do not specify a database, the default database \'()\' is assumed.|
| \\h                              | Print help information.|
| \\dd [NAME]                    | Lists the tables present in the  database specified by NAME. If you do not specify a database, tables in the default database    \'()\' are displayed.            |
| \\d[t] NAME                    | Lists the fields and references  in the table specified by NAME.  |
| \\ld [PATTERN]                 | Lists the databases in the Gaia  catalog. Optionally, you can filter the results by specifying a regex pattern.  <p>For more information on regex, see [ECMAScript syntax](http://www.cplusplus.com/reference/regex/ECMAScript/%22%3E) on the [cplusplus.com](http://cplusplus.com/)  website. </p>|
| \\lf [PATTERN]                 | Lists the data fields in the  Gaia catalog. Optionally, you can filter the results by specifying a regex pattern. <p>For more information on regex, see [ECMAScript syntax](http://www.cplusplus.com/reference/regex/ECMAScript/%22%3E) on the [cplusplus.com](http://cplusplus.com/)  website.  </p> |
| \\lr [PATTERN]                 | Lists the relationships in the Gaia catalog. Optionally, you can filter the results by specifying a regex pattern. <p>For more information on regex, see [ECMAScript syntax](http://www.cplusplus.com/reference/regex/ECMAScript/%22%3E) on the [cplusplus.com](http://cplusplus.com/)  website. </p>|
| \\l[t] [PATTERN]             | Lists the tables in the Gaia catalog. Optionally, you can filter the results by specifying a regex pattern.  <p>For more information on regex, see [ECMAScript syntax](http://www.cplusplus.com/reference/regex/ECMAScript/%22%3E) on the [cplusplus.com](http://cplusplus.com/)  website. </p>|
| \\q                              | Quit |


Gaiac has three modes of operation:

- Loading: By default without specifying any mode, gaiac runs in loading mode. In this mode, gaiac reads the specified DDL file and translates them into catalog records without generating the Direct Access heard files.
- Interactive : (--interactive or -i) provides a command-line interface that you can use to try DDL statements without creating a DDL file. DDL statements entered on the command line are executed and any output is displayed to the console.
- Generation: (--generate or -g), In this mode, gaiac generates the Direct Access header files based on the specified database(s) to the specified output path

## Next steps

Learn more about the [Gaia Data Definition Language](../reference/ddl-gaia.md)
