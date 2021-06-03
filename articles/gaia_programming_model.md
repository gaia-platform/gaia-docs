---
author: 
owner: 
lastupdate: 
---

## Gaia programming model

Gaia’s Declarative C++ operates on Active Fields that are described in the Catalog. Active Fields are references to the columns described in the database schema. In addition, you can define local C++ variables to provide temporary storage during the processing of a rule. For clarity, this document refers to local C++ variables simply as variables and Active Fields as field references.

Gaia enables you to write multi-threaded applications with components that execute asynchronously and in parallel with each other. When multiple database events, either insertion of or changes to fields, occur simultaneously, any corresponding rules associated with those fields fire automatically in parallel.

Binding Rulesets with a simple declaration (SerialStream(name)) to a named thread allows the author to ask for some degree of synchronicity.

## Ruleset Structure

Rules are created by authoring text files with the extension. Ruleset. Rulesets are defined with the `Ruleset(name) { . . . }` syntax. Each Ruleset name must be unique so that it can be tracked and managed through the Catalog. Rulesets should encapsulate a set of  rules *for a common purpose*. That purpose can be anything from the rules for a small application to independent units of business or application logic.

## Rule structure

An individual Rule can be either single or multi-statement. Each Rule is bracketed {. . .} to clearly delineate the scope of each rule.

The body of a Rule contains C++ statements and field references. The C++ is unrestricted, so any valid statements are permitted. The way to think about references is that they are predefined variable references to fields defined in the Catalog that are globally visible to the rules you define. The reference is to either the ‘current’ row for which the rules engine fires the Rule (the *Anchor Row*) or the row(s) referenced in another table with a relationship with the Anchor Row. The Anchor Row specifies the starting point for the data that the Rule processes.

## Active Fields

An Active Field is a reference to a field defined in the Gaia Catalog that causes any Rule containing the field reference to execute when its value changes in the database. You can designate any field defined in the Catalog as active in your rules.

You can specify which fields are active in two ways:

-   By prepending an @ symbol to the field:  
    
```cpp
    if (@person.location == some\_location) …

```

-   By specifying fields in an OnUpdate or OnChange attribute:
  
```cpp
    OnChange(person.location){
    ...
    }
```

Many of the rules you will write will be very short with one or two Active Fields, and in those cases, using the @ syntax can keep your code readable and concise. If your Rule is more complex, keeping track of the field references using the @ identifier method can become confusing. In this case, identifying all of the field references using an 'On' attribute keeps your rules cleaner and allows you to identify all of the Active Fields at a glance.

**NOTE**: These two methods of specifying the field references in a Rule are mutually exclusive. If you inadvertently mix them, gaiat notifies you with the following error message: "Since a Rule attribute was provided, specifying Active Fields inside the Rule is not supported."

At the beginning of each Ruleset, you can use the optional Table attribute to specify which tables in the Catalog that Gaia uses to disambiguate field references:

**Table(table1, table2, ...)**: This helps ensure disambiguation immediately and later if you add other tables with the same field names to the database.

The table name is a qualifier specifying the table that contains the field name being referenced. A period ‘.’ is required to separate this qualifier from the rest of the reference. This qualifier is optional and can be omitted whenever the associated field name is unique within the table scope for this Ruleset.

## Field Names

Every field reference is to a field of a record in a table. To ensure that your field names are unambiguous, qualify them with the table name (Passenger.MilesFlown.) However, Gaia also allows for unqualified field names. If a field name is unique across all tables, you can use it without qualification. If a field name occurs in more than one table other than in a key reference, you must qualify it with the table name. The translation engine flags cases when the qualification is necessary. 
As a database grows, a previously unique field name can become ambiguous. The Gaia translation engine reports these cases out so that you can decide how to address them.

## OnXxxx Rule prefixes

The OnXxxx prefix specifies the fields or tables that cause the Rule to execute when a record or a field changes. If the fields are unique in the Catalog, you can omit specifying the table in which they are defined. In either case, all of the fields specified must be defined in the same table.

The arguments to OnXxxx consist of a set of table and field references. The Rule `OnChange(Traveller){. . . }` causes the Rule to fire whenever a record changes in the Traveller table. The Rule `OnUpdate(Traveller.MemberMiles, Traveller.MemberLevel){. . .}` causes the Rule to fire when either of the fields change.

There are three forms of On:

1. `OnUpdate(field1, field2, …, fieldn){ . . . }` or `OnUpdate(table){ . . . }`: Reacts to the change of the specified fields in existing records in a table or a change to a specified table. If the fields are unique in the Catalog, you can omit specifying which table they are in.
2. `OnChange(field1, field2,..., fieldn){ . . . }` or `OnChange(table){ . . . }`: Reacts to the Insertion or change of a record that contains the specified fields in the specified table.
3. `OnInsert(table){ . . . }`: Reacts to the insertion of a new record. Unlike the other On prefixes, OnInsert takes a single parameter.

```cpp
    // Rule 1: Whenever a name is inserted,
    // insert a new greeting into the greetings table.
    OnInsert(names)
    {
        // Form the greeting using the name.
        string new\_greeting = "Hello " + string(names.name) + "!";
        // Insert the greeting.
        gaia::hello::greetings\_t::insert\_row(new\_greeting.c\_str());
    }
```

The OnXxxx prefix provides a finer-grained control on the kind of data change that fires a rule.
