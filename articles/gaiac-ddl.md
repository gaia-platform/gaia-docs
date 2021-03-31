---
author: 
owner: 
lastupdate: 
---

# Gaiac and DDL

---

**NOTE**

The information contained in this document represents information about prerelease features of the product. Features might change when the product is released for general availability.

---

The Gaia Catalog Tool (gaiac) creates the datastore and tables that support your application. It also translates Gaia DDL files into the Gaia headers you include in your ruleset and app code files.

## Usage

Usage:gaiac \[options\] \[ddl\_file\]`

Command line arguments

| Argument | Description  |
|---|---|
| -d &lt;dbname&gt; | Set the database name. |
| -i | Opens gaiac in interactive mode. For more information on the available commands, see the Interactive mode commands section below. |
| -g | <p>Generate the Gaia headers and database tables for the specified ddl file.</p> <p>If the -d argument does not specify the database name, gaiac uses the ddl file name as the database name it creates.</p> <p>If the database does not exist, gaiac automatically creates it.<br /> If you do not specify a database name or a ddl file, the</p> |
| -o &lt;path&gt; | Sets the output path for the generated header files. |
| -h | Print help information. |
| &lt;ddl file name&gt; | <p>Specifies the ddl file from which to create database tables.<br /> If the -d argument does not specify the database name, gaiac uses the ddl file name as the database name it creates.</p> <p>If the database does not exist, gaiac automatically creates it.</p> |

## Interactive mode commands

In the interactive mode the following commands are available:

| Command | Description |
|---|---|
| CREATE DATABASE [if not exists] DATABASE_NAME | Creates a database with the specified database name. If the command succeeds, gaiac returns to the prompt. If the command fails, gaiac returns an error message and returns to the prompt. |
| CREATE [if not exists] [DATA_BASE NAME.]TABLE ( &lt;field definitions&gt;) | Creates a table with the specified name and fields. If the command succeeds, gaiac returns to the prompt. If the command fails, gaiac returns an error message and returns to the prompt. <br /> For more information on table specifications, see the CREATE TABLE section below.|
| DROP DATABASE NAME | Removes the database specified by NAME from the catalog. |
| DROP TABLE [DATA_BASE NAME.]TABLE_NAME | Removes the table specified by TABLE_NAME from the specified database specified by TABLE_NAME. If you do not specify a database, the default database '()' is assumed. |
| \h | Print help information. |
| \dd [NAME] | Lists the tables present in the database specified by NAME. If you do not specify a database, tables in the default database '()' are displayed. |
| d[t] NAME | Lists the fields and references in the table specified by NAME. |
| \ld [PATTERN] | Lists the databases in the Gaia catalog. Optionally, you can filter the results by specifying a regex pattern. For more information on regex, see [ECMAScript syntax](http://www.cplusplus.com/reference/regex/ECMAScript/">) on the cplusplus.com website. |
| \lf [PATTERN] | Lists the data fields in the Gaia catalog. Optionally, you can filter the results by specifying a regex pattern. For more information on regex, see [ECMAScript syntax](http://www.cplusplus.com/reference/regex/ECMAScript/">) on the cplusplus.com website. |
| \lr [PATTERN] | Lists the relationships in the Gaia catalog. Optionally, you can filter the results by specifying a regex pattern. For more information on regex, see [ECMAScript syntax](http://www.cplusplus.com/reference/regex/ECMAScript/">) on the cplusplus.com website. |
| \l[t] [PATTERN] | Lists the tables in the Gaia catalog. Optionally, you can filter the results by specifying a regex pattern. For more information on regex, see [ECMAScript syntax](http://www.cplusplus.com/reference/regex/ECMAScript/">) on the cplusplus.com website. |
| \q | Quit |

## Data Definition Language

The Gaia DDL is similar to SQL since many of the platform features, such as tables, index, and constraints, map naturally to the similar or equivalent relational database features.

### CREATE TABLE

Use the CREATE TABLE command to create a table.

```sql
CREATE TABLE [if not exists] *table\name* ( [
{ *fieldname* *datatype*
[, ... ]
] )
```

The following example creates a table named "department."

```sql
CREATE TABLE if not exists department ( name string,current bool active);
```

Active Fields are Database Column names that you refer to in the body of a Declarative Rule expression. When your declarative code refers to one of these Fields with a read operation, Gaia triggers an Event that schedules the associated Declarative Rules to execute.

### REFERENCES

Use the REFERENCES keyword to specify a foreign key constraint when creating tables.

The following example creates a table named "employee." The "department" field has a foreign key constraint pointing to the department.

```sql
CREATE TABLE if not exists employee (
   name string,
   department references department
);
```
