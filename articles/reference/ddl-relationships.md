---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Relationships

Relationships in the Gaia Database allow a row in one table to reference one or more rows in another table. There are two ways to create a relationship between tables:

* Explicit Relationships
* Implicit Relationships

## Explicit Relationships

To create an Explicit Relationship you link the rows using the [connect()](ddl-connect-disconnect.md) method. To unlink the row you call the [disconnect()](ddl-connect-disconnect.md) method. The relationship is considered *explicit* because you specify links between specific rows in the database, independently of the data in those rows.

## Implicit Relationships

To associate two tables with an Implicit Relationship, specify a Value Linked Reference in the Data Definition Language (DDL) file. When two rows in different tables contain the same value in their linked fields, a link is automatically created between the rows and a relationship is created between the tables. The relationship is considered *implicit* because links between rows are data-dependent: two rows are linked if the values in their linked fields are identical.

For more information on Implicit Relationships, see Implicit [Relationships](ddl-implicit-relationships.md).

## create

Creates a one-to-one or a one-to-many link between tables.

```
[create] relationship [if not exists] relationship_name (
    table_name_1.field_name_1 -> table_name_2[],
    table_name_2.field_name_2 -> table_name_1
    [, USING table_name_2(field_name_3,...), table_name_1(field_name_4, ...)]
);
```

Removes the specified relationship from the catalog.

The short form of the `create relationship` statement, in which you omit the create operator, drops the table before attempting to create it and has the same effect as the following:

`drop relationship if exists foo; create relationship relationship_name;`

## drop 

`drop relationship_name;`

The relationship statement adds virtual fields to each of the specified tables.

The following examples create relationships named "incubator_sensors" and "incubator_actuators".

```
create relationship if not exists incubator_sensors (
    incubator.sensors -> sensor[],
    sensor.incubator -> incubator
);
 
create relationship if not exists incubator_actuators (
    incubator.actuators -> actuator[],
    actuator.incubator -> incubator
);

```
In incubator_sensors the incubator.sensors -> sensor[] statement adds a virtual field to the incubators table that links to the sensor.incubator -> incubator  statement  adds a virtual field to the sensor table that links back to the incubator table.

To create a one to one relationship omit the brackets ([]). 

`incubator.sensors -> sensor`
 
Use the interactive feature of gaiac to list the instantiated relationships:

<pre>
gaiac> \lr
+----------------------------------+--------------------------+--------------------------------------------+----+
| Name                             | Parent (link)            | Child (link)                               | ID |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_database_table      | gaia_database (database) | gaia_table (gaia_tables)                   | 10 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_table_field         | gaia_table (table)       | gaia_field (gaia_fields)                   | 19 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_relationship_parent | gaia_table (parent)      | gaia_relationship (outgoing_relationships) | 32 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_relationship_child  | gaia_table (child)       | gaia_relationship (incoming_relationships) | 33 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_ruleset_rule        | gaia_ruleset (ruleset)   | gaia_rule (gaia_rules)                     | 42 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_table_index         | gaia_table (table)       | gaia_index (gaia_indexes)                  | 48 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| incubator_sensors                | incubator (incubator)    | sensor (sensors)                           | 72 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| incubator_actuators              | incubator (incubator)    | actuator (actuators)                       | 73 |
+----------------------------------+--------------------------+--------------------------------------------+----+
</pre>