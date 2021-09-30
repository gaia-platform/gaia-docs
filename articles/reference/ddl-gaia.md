---
author: Don Glover
owner: Don Glover
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Gaia Data Definition Language

The Gaia Data Definition Language (DDL) is similar to SQL; many of the platform’s features, such as tables, indexes, and constraints, map naturally to similar or equivalent relational database features.
The structure of a DDL file is:

```
/* create database is optional */
[CREATE] DATABASE [if not exists] database_name;
 
/* use is optional */
USE database_name;
 
[CREATE] TABLE [if not exists] table_name ( [
{ fieldname datatype [UNIQUE]
[, ... ]
] );
```

NOTE: The `create` keyword is optional.
 
## Use of semicolons

Semicolons separate statements in the DDL into logical groupings of create statements. A sequence of statements ending in a semicolon can contain forward references to subsequent statements in the group of statements and any logical groupings that precede it. It can not refer to logical groupings that follow it. 

Semicolons in the DDL text are optional as long as the DDL file contains only create statements. If semicolons are omitted from the DDL, gaiac compiles the file as if there is a semicolon at the end of the file.  No forward references are possible to definitions currently in the schema.

## Comments in DDL files

Single line comment

```
/* comment */

or

-- comment
```

Block comment

```
/** 
	Comment
**/
```