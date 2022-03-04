---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

# Building apps with Gaia

After your design phase, the Gaia Platform architecture lends itself best to the following workflow:

- Create a schema for your application.
  - The schema defines tables and fields that contain the state of your system at any given time.
    - Add tables to manage the state of your application.
    - Determine which columns in the schema drive the behavior. Your Rules act on these fields.
- In your CMakeLists.txt run gaiac to import the schema and create tables for your database.
  - Gaiac generates a header that contains the Direct Access Classes (DAC) that map your schema in code. You include in this header your Ruleset definition file.
- Create your Ruleset.
  - Identify the actions to be performed when a field changes.
- Run gaiat to translate the Ruleset into a C++ file.
- Create your application and add the gaiat generated C++ file in your app.

The Rules Engine generates events when changes are committed to the database. For instance, if a database table stores the state of a sensor, the Rules Engine fires an update/insert event in response to the change of the sensor value.
Currently system only supports insert and update events. Events are defined at the source of the change (a sensor's input stream, a schema entry) and a type of event.

This is, of course, an iterative process. As you refine your application, you will make changes to the schema and Ruleset. A good start is to add the input table with an Active Field, then add a Ruleset that contains a Rule that fires when rows are inserted. Build from there (iterate) with tables for output and managing the application's state.

You define the schema in a Data Definition Language (DDL)file.

```sql
create table if not exists names
(
    name string
);

create table if not exists greetings
(
    greeting string
);
```

When you run gaiac, the composer creates the tables in the catalog and outputs header files that you include in your application.

When designing your solution for the Gaia Platform, keep in mind that each Rule runs within a separate OS thread, and each thread can only have one outstanding transaction at a time. This provides transaction isolation which means, two simultaneously running Rules (each with a unique thread and transaction) will never see each other's changes. When a rule successfully executes, it automatically commits its transaction. If an exception is thrown within the rule body, the rule engine catches the exception and rollback the current transaction.

To state this another way:

- Rules run on separate threads. When data identified by an Active Field changes, it is possible for your app to check the database before Rules based on the field run.
- Rules processing is transactional, a Rule must complete before you can see the results of actions due to the changes.
- Rules only fire after the transaction that contains the change to the [Active Field](gaia-glossary.md#active-fields-rules-engine) is committed. Said more succinctly, Rules run post-commit.

## See Also

[Gaia Data Definition Language](reference/ddl-gaia.md)
