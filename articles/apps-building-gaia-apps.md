---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

# Building apps with Gaia

After your design phase, the Gaia Platform architecture lends itself best to the following workflow:

-   Create a schema for your application.
    -   The schema defines tables and fields that contain the state of your system at any given time.
    -   Add tables to manage the state of your application.
    -   Determine which columns in the schema drive the behavior. Your rules will act on these fields.
-   In your makefile run gaiac to import the schema and create tables for your database.
    -   Gaiac generates a header that contains the edc classes that define your schema in code. You include in this header your Ruleset definition file.
-   Create your Ruleset.
    -   Identify the actions to be performed when an Active Field changes.
-   Run gaiat to translate the Ruleset into code files that you  in your app.
-   Create your application and call the code supplied by gaiat.

Describes input to the system that Gaia uses to fire a Rule. Events can be generated from incoming sensor data, Database operations (such as a commit), Field updates; pretty much anything that you can attach to a processing action (a Rule). Events are defined at the source of the change (a sensor's input stream, a schema entry) and a type of event. The event also names a rRuleule that is to be fired when the specific type of event occurs.
This is, of course, an iterative process. As you refine your application, you will make changes to the schema and Ruleset. A good start is to add the input table with an Active Field, then add a Ruleset that contains a Rule that fires when rows are inserted. Build from there (iterate) with tables for output and managing the application's state.

You define the schema in a DDL file.

```
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

When designing your solution for the Gaia Platform, keep in mind that each Rule runs within a separate OS thread, and each thread can only have one outstanding transaction at a time. This provides transaction isolation which means, two simultaneously running rules (each with a unique thread and transaction) will never see each other's changes. A Rule must complete executing, which automatically commits its transaction before the actions resulting from the Rule can be seen by the rest of the system.

To state this another way:

-   Rules run on separate threads. When data identified by an Active Field changes, it is possible for your app to check the database before rules based on the field run.
-   Rules processing is atomic, a Rule must complete before you can see the results of actions due to the changes.
-   Rules only fire after the transaction that contains the change to the [Active Field](gaia-glossary.md#active-fields-rules-engine) is committed. Said more succinctly, rules run post-commit.
