---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---
# create table

Creates a new table. If the table already exists, gaiac returns an error and the table is not created.

```
[CREATE] TABLE [if not exists] table_name ( [
{ fieldname datatype [unique][optional]
[, ... ]
] );
```

The if not exists operator is optional. If a table with the same name already exists, the command is ignored. However, there is no verification that the existing table has a structure identical to that indicated by the create table statement.

The fieldname parameter specifies the name of a field in the table.

The datatype parameter specifies the type of data the field holds (e.g. varchar, integer, date, etc.).

Valid data types are:

- bool - Boolean data type
- int8 - 8-bit integer
- Uint8 - unsigned 8-bit integer
- Int16 - 16-bit integer
- uint16 - unsigned 16-bit integer
- int32 - 32-bit integer
- uint32 - unsigned 32-bit integer
- int64 - 64-bit integer
- uint64 - unsigned 64-bit integer
- float - signed 4-byte floating-point number
- double - signed 8-byte floating-point number
- string - A null-terminated vector of bytes up to the limit of the available payload. Typically ASCII or utf-8.
- Arrays of scalar types. For example, `history int32[]`. For more information, see [Scalar Arrays](declarative-scalar-arrays.md).

## Remarks

Use the optional `if not exists' to prevent an error from occurring if the table exists. If a table with the same name already exists, the command is ignored. However, there is no verification that the existing table has a structure identical to that indicated by the CREATE TABLE statement.

The short form of the `create table` statement, in which you omit the create operator, drops the table before attempting to create it and has the same effect as the following:

`drop table if exists table_nam; create table table_name;`

The following example creates a table named "department."

```sql
CREATE TABLE if not exists department ( name string, current bool active);
```

### Optional values

When you declare an optional value in your Data Definition Language (DDL), you can specify both optional and unique.

Null values are not considered when evaluating values.

Fields that are speicified as optional differ from non-optional in the following manner:

- If you do not use the optional keyword, Gaia assigns a default value.
- If you use the option keyword, Gaia assigns a null value.

Optional values in Gaia are implemented in the Direct Access Classes using containers that provide functionality similar to  C++17 optional values. You can expect an optional value to behave like a C++17 optional.

The following table declares the height field as optional.

```sql
    table patient (
        name string,
        height uint8 optional,
        is_active bool,
        analysis_results float[],
        doctor references doctor,
        address references address
    )
```

The following examples show inserting a row that contains no value for the height field and then accessing that field to include it in the log output.

```cpp
    void optional_values()
    {
        // You can pass nullopt instead of height to denote the absence of value.
        gaia_id_t id = patient_t::insert_row("John", gaia::common::nullopt, false, {});
        patient_t john = patient_t::get(id);
    
        // john.height() return a gaia::common::optional_t<uint8> which behaves
        // similarly to a C++17 std::optional.
        if (john.height().has_value())
        {
            throw std::runtime_error("The value for john.height() should be missing.");
        }
        else
        {
            gaia_log::app().info("No height provided for patient {}", john.name());
        }
    
        // You can update the missing value with a value.
        patient_writer john_w = john.writer();
        john_w.height = 178;
        john_w.update_row();
    
        if (john.height().has_value())
        {
            gaia_log::app().info("{}'s height is {}", john.name(), john.height().value());
        }
        else
        {
            throw std::runtime_error("The height column is supposed to have a value.");
        }
    }
```

Use the interactive feature of gaiac to list the instantiated tables:

<pre>
gaiac> \lt
+-----------+-------------------+-----+------------+
| Database  | Name              | ID  | Type       |
+-----------+-------------------+-----+------------+
| catalog   | gaia_database     | 2   | 4294967291 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_table        | 4   | 4294967294 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_field        | 11  | 4294967295 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_relationship | 19  | 4294967290 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_ruleset      | 31  | 4294967292 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_rule         | 37  | 4294967293 |
+-----------+-------------------+-----+------------+
| event_log | event_log         | 41  | 4294963200 |
+-----------+-------------------+-----+------------+
| hello     | names             | 126 | 9          |
+-----------+-------------------+-----+------------+
| hello     | greetings         | 128 | 10         |
+-----------+-------------------+-----+------------+
| hello     | another_table     | 131 | 11         |
+-----------+-------------------+-----+------------+
| catalog   | gaia_index        | 134 | 4294967289 |
+-----------+-------------------+-----+------------+
| flights   | airplane          | 141 | 9          |
+-----------+-------------------+-----+------------+
| flights   | route             | 144 | 10         |
+-----------+-------------------+-----+------------+
| flights   | flight            | 147 | 11         |
+-----------+-------------------+-----+------------+
| flights   | segment           | 154 | 12         |
+-----------+-------------------+-----+------------+
| flights   | trip              | 158 | 13         |
+-----------+-------------------+-----+------------+
| flights   | traveller         | 161 | 14         |
+-----------+-------------------+-----+------------+
</pre>

Gaia provides two ways to connect data in your tables:

- Explicitly connect tables in your Ruleset code
- Use references to common fields in your DDL

To explicitly connect two tables using references you use the connect statement in your Ruleset code to establish the relationship between the tables. For more information about the connect and disconnect statements, see [connect and disconnect](declarative-connect-disconnect.md).

Value Linked References allow to you create links between tables. You can establish a relationship between two tables by specifying fields that, when set to the same value, allows Gaia to automatically relate rows in those tables to each other in either a 1:1 or 1:M relationship. For more information on Value Linked References, see [Common fields](ddl-implicit-relationships.md).

You can have 4 types of connections between tables defined in the DDL:

- A 1:1 relationship which is unidirectional
- A 1:N relationship which is unidirectional
- A 1:1 relationship which is bi-directional
- A 1:N relationship which is bi-directional
