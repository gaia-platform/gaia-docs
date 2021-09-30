---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

## Gaia programming model

Gaia's Declarative C++ operates on Active Fields that are described in the Catalog. Active Fields are references to the columns described in the database schema. In addition, you can define local C++ variables to provide temporary storage during the processing of a Rule. For clarity, this document refers to local C++ variables simply as variables and Active Fields as field references.

Gaia enables you to write multi-threaded applications with components that execute asynchronously and in parallel with each other. When multiple database events, either insertion of or changes to fields, occur simultaneously, any corresponding rules associated with those fields fire automatically in parallel.

Binding Rulesets to a named thread using the SerialStream method allows only one Rule in the SerialStream to execute at a time. This allows each Rule in the SerialStream to see the committed changes of the previous one.

A Rule operates in the context of an anchor row from the database. It is the row that changes that prompted the Rule to fire. The definition of the table for this anchor row drives the interpretation of all references to fields and anchor-related tables in the database. There can be only one anchor specified for a Rule. 

## Active Fields

An Active Field is a reference to a field defined in the Gaia Catalog that causes any Rule containing the field reference to fire when its value changes in the database. You can designate any field defined in the Catalog as active in your rules.

You can specify which fields are active in two ways:

- By specifying fields in an on_update or on_change attribute:
    ```
    on_change(person.location){
    ...
    }
    ```
- By prepending an @ symbol to the field: 
    `if (@person.location == some_location) …`

Many of the rules you will write will be very short with one or two Active Fields, and in those cases, using the @ syntax can keep your code readable and concise. If your Rule is more complex, keeping track of the field references using the **@** identifier method can become confusing. In this case, identifying all of the field references using an 'On' attribute keeps your rules cleaner and allows you to identify all of the Active Fields at a glance.

All active fields must be part of the same table in a single Rule. Gaia Platform does not support "multi-anchor" rules.

**NOTE**: These two methods of specifying the field references in a Rule are mutually exclusive. If you inadvertently mix them, gaiat notifies you with the following error message: "Since a Rule attribute was provided, specifying Active Fields inside the Rule is not supported."

At the beginning of each Ruleset, you can use the optional Table attribute to specify which tables in the Catalog that Gaia uses to disambiguate field references:

table(table1, table2, ...): This helps ensure disambiguation immediately and later if you add other tables with the same field names to the database.

The table name is a qualifier specifying the table that contains the field name being referenced. A period '.' is required to separate this qualifier from the rest of the reference. This qualifier is optional and can be omitted whenever the associated field name is unique within the table scope for this Ruleset.

## Ruleset Structure

Rules are created by authoring text files with the extension .ruleset. In the source file, Rulesets are defined using the `ruleset name  { . . . }` syntax. Each Ruleset name must be unique so that it can be tracked and managed through the Catalog. Rulesets should encapsulate a set of rules for a common purpose. That purpose can be anything from the rules for a small application to independent units of business or application logic.

## Rule structure

Each Rule is bracketed `{. . .}` to delineate the scope of the Rule.

The body of a Rule contains a mix of C++ statements and declarative statements. The C++ is unrestricted, so any valid statements are permitted. The declarative statements can contain field references. The way to think about references is that they are predefined variable references to fields defined in the Catalog that are globally visible to the rules you define. The reference is to either the 'current' row for which the rules engine fires the Rule (the Anchor Row) or the row(s) referenced in another table with a relationship with the Anchor Row. The Anchor Row specifies the starting point for the data that the Rule processes.

The general structure is:

```
<filename>.ruleset
ruleset <rulesetname1>
{
    { rule code }

    { rule code }
}

ruleset <rulesetname2>
{
    { rule code }

    { rule code }
}
```

## Field Names

Every field reference is to a field of a row in a table. To ensure that your field names are unambiguous, qualify them with the table name. However, Gaia also allows for unqualified field names.  If a field name is unique across all tables, you can use it without qualification. If a field name occurs in more than one table other than in a key reference, you must qualify it with the table name. The translation engine flags cases when the qualification is necessary.

As the definition of your database changes and expands, a previously unique field name can become ambiguous. The Gaia translation engine reports these cases out so that you can decide how to address them.

## on_xxxx Rule prefixes

The on_xxxx prefix specifies the fields or tables that cause the Rule to fire when a row or a field changes. If the fields are unique in the Catalog, you can omit specifying the table in which they are defined. In either case, all of the fields specified must be defined in the same table.

The arguments to on_xxxx consist of a set of table and field references. The Rule on_change(Traveller){. . . } causes the Rule to fire whenever a row changes in the Traveller table. The Rule on_update(Traveller.MemberMiles, Traveller.MemberLevel){. . .} causes the Rule to fire when either of the fields change.

There are three forms of On:

1. [on_update](reference/declarative-on_update.md)(field1, field2, …, fieldn){ . . . }  or on_update(table){ . . . }: Reacts to the change of the specified fields in existing rows in a table or a change to a specified table. If the fields are unique in the Catalog, you can omit specifying which table they are in.
1. [on_change](reference/declarative-on_change.md)(field1, field2,..., fieldn){ . . . } or on_change(table){ . . . }: Reacts to the Insertion or change of a row that contains the specified fields in the specified table. 
1. [on_insert](reference/declarative-on_insert.md)(table){ . . . }: Reacts to the insertion of a new row. Unlike the other On prefixes, on_insert takes a single parameter. 
    ```
    // Rule 1: Whenever a name is inserted,
    // insert a new greeting into the greetings table.
    on_insert(names)
    {
        // Form the greeting using the name.
        string new_greeting = "Hello " + string(names.name) + "!";

        // Insert the greeting.
        gaia::hello::greetings_t::insert_row(new_greeting.c_str());
    }
    ```

The on_xxxx prefix provides a finer-grained control on the kind of data change that fires a Rule. Additionally, the on_xxxx prefixes are more flexible than the '@'. They allow you to define rules that address active fields that are part of an Update vs an Insert, whereas '@' always behaves like on_change.

You might also find that as rules grow more complex, the on_xxxx() prefix aids in readability and understanding among different developers.