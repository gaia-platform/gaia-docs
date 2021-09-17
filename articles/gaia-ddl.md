---
author: Don Glover
owner: Don Glover
lastupdate: 
---

# Gaia Data Definition Language

The Gaia DDL is similar to SQL; many of the platform's features, such as tables, indexes, and constraints, map naturally to similar or equivalent relational database features.

Use of semicolons

The format of a DDL file is...

### **CREATE DATABASE**

Creates a new database. If the database already exists, gaiac returns an error and the database is not created.

[CREATE] DATABASE [if not exists] database_name;

The CREATE keyword is optional.

The if not exists operator is optional. If a database with the same name already exists, the command is ignored. However, there is no verification that the existing database has a structure identical to that indicated by the CREATE DATABASE statement.

Use the interactive feature of gaiac to list the instantiated databases:

gaiac> \\ld

<pre>
+-----------+-----+
| Name      | ID  |
+-----------+-----+
| catalog   | 1   |
+-----------+-----+
| event_log | 40  |
+-----------+-----+
| hello     | 125 |
+-----------+-----+
| flights   | 140 |
+-----------+-----+
</pre>

### **DROP DATABASE**

Removes the database from the catalog.

DROP DATABASE database_name;

### **USE**

Specifies the database to use when compiling the DDL.

USE database_name;

### **CREATE TABLE**

Creates new a table. If the table already exists, gaiac returns an error and the table is not created.

```sql
[CREATE] TABLE [if not exists] table_name ( [
{ fieldname datatype [UNIQUE]
[, ... ]
] );
```

The CREATE keyword is optional.

The if not exists operator is optional. If a table with the same name already exists, the command is ignored. However, here is no verification that the existing table has a structure identical to that indicated by the CREATE TABLE statement.

The fieldname parameter specifies the name of a field in the table.

The datatype parameter specifies the type of data the field holds (e.g. varchar, integer, date, etc.).

Valid data types are:

-   bool -
-   int8 - 8-bit integer
-   Uint8 - unsigned 8-bit integer
-   Int16 - 16-bit integer
-   uint16 - unsigned 16-bit integer
-   int32 - 32-bit integer
-   uint32 - unsigned 32-bit integer
-   int64 - 64-bit integer
-   uint64 - unsigned 64-bit integer
-   float - signed 4-byte floating-point number
-   double - signed 8-byte floating-point number
-   string - A null-terminated vector of bytes up to the limit of the
    available payload. Typically ASCII or utf-8.

Optional `unique` operator.

Remarks

Use the optional `if not exists` to prevents an error from occurring if the table exists. If a table with the same name already exists, the command is ignored. However, there is no verification that the existing table has a structure identical to that indicated by the CREATE TABLE statement.

The following example creates a table named "department."

```sql
CREATE TABLE if not exists department ( 
    name string,
    current bool 
);
```

Active Fields are Database Column names that you refer to in the body of a Declarative Rule expression. When your declarative code refers to one of these Fields with a read operation, Gaia fires an Event that schedules the associated Declarative Rules to execute.

Use the interactive feature of gaiac to list the instantiated tables:

gaiac\> \\lt

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| Database \| Name \| ID \| Type \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| catalog \| gaia_database \| 2 \| 4294967291 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| catalog \| gaia_table \| 4 \| 4294967294 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| catalog \| gaia_field \| 11 \| 4294967295 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| catalog \| gaia_relationship \| 19 \| 4294967290 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| catalog \| gaia_ruleset \| 31 \| 4294967292 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| catalog \| gaia_rule \| 37 \| 4294967293 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| event_log \| event_log \| 41 \| 4294963200 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| hello \| names \| 126 \| 9 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| hello \| greetings \| 128 \| 10 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| hello \| another_table \| 131 \| 11 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| catalog \| gaia_index \| 134 \| 4294967289 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| flights \| airplane \| 141 \| 9 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| flights \| route \| 144 \| 10 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| flights \| flight \| 147 \| 11 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| flights \| segment \| 154 \| 12 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| flights \| trip \| 158 \| 13 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

\| flights \| traveller \| 161 \| 14 \|

+\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\--+

#### REFERENCES

Forward References allow an identifier to reference an entity whose definition appears after the identifier. In other words, the order of the data definition statements does not matter.

In Gaia DDL you build a statement that includes Forward References by including all of the statement segments in a single statement terminated by a semicolon. For example, the following DDL segment defines two tables: doctor and patient. The doctor table creates a reference to the patient table, which is defined after the doctor table.

```sql
**create** **table** doctor (
**name** **string**,
patients **references** patient[]
)
**create** **table** patient (
**name** **string**,
doctor **references** doctor.patients
);
```

If the doctor table definition were terminated with a semicolon the compilation of the DDL would fail since the patient table has not yet been defined.

### 

### **DROP TABLE**

Removes the table from the catalog.

DROP TABLE table_name;

### **RELATIONSHIPS**

Creates a one to one or a one to many link between tables.

CREATE RELATIONSHIPS [if not exists] relationship_name (

table_name_1.field_name_1 -\> table_name_2[],

table_name_2.field_name_2 -\> table_name_1

[, **USING** table_name_2(field_name_3,\...),
table_name_1(field_name_4, \...)]

);

The relationship statement adds virtual fields to each of the specified
tables.

The following examples create relationships named \"incubator_sensors\"
and \"incubator_actuators\".

create relationship if not exists incubator_sensors (

incubator.sensors -\> sensor[],

sensor.incubator -\> incubator

);

create relationship if not exists incubator_actuators (

incubator.actuators -\> actuator[],

actuator.incubator -\> incubator

);

In incubator_sensors the incubator.sensors -\> sensor[] statement adds
a virtual field to the incubators table that links to\
The sensor.incubator -\> incubator statement adds a virtual field to the
sensor table that links back to the incubator table.

To create a one to one relationship omit the brackets ([]).

incubator.sensors -\> sensor

Use the interactive feature of gaiac to list the instantiated
relationships:

gaiac\> \\lr

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

\| Name \| Parent (link) \| Child (link) \| ID \|

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

\| gaia_catalog_database_table \| gaia_database (database) \| gaia_table
(gaia_tables) \| 10 \|

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

\| gaia_catalog_table_field \| gaia_table (table) \| gaia_field
(gaia_fields) \| 19 \|

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

\| gaia_catalog_relationship_parent \| gaia_table (parent) \|
gaia_relationship (outgoing_relationships) \| 32 \|

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

\| gaia_catalog_relationship_child \| gaia_table (child) \|
gaia_relationship (incoming_relationships) \| 33 \|

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

\| gaia_catalog_ruleset_rule \| gaia_ruleset (ruleset) \| gaia_rule
(gaia_rules) \| 42 \|

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

\| gaia_catalog_table_index \| gaia_table (table) \| gaia_index
(gaia_indexes) \| 48 \|

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

\| incubator_sensors \| incubator (incubator) \| sensor (sensors) \| 72
\|

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

\| incubator_actuators \| incubator (incubator) \| actuator (actuators)
\| 73 \|

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+\-\-\--+

##  

## **Example DDL**

create database if not exists StudentRecords;

use StudentRecords;

create table Student (

StudentId string,

Surname string,

Age int32,

TotalHours int32,

GPA float

);

create table Course (

CourseId string,

Name string,

Hours int32

);

create table Registration (

RegId string,

Status string,

Grade string,

references Student

);

create table PreReq (

PreReqId string,

MinGrade string

);

create relationship student_registrations (

Student.registrations-\>Registration[],

Registration.registered_student-\>Student

);

create relationship course_registrations (

Course.registrations-\>Registration[],

Registration.registered_course-\>Course

);

create relationship course_prerequisites (

Course.prerequisites-\>PreReq[],

PreReq.prerequisite_of-\>Course

);

create relationship prerequisite_courses (

Course.prerequisite_of-\>PreReq[],

PreReq.prerequisites-\>Course

);
