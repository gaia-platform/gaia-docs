---
author: 
owner: 
lastupdate: 
---

# Gaia Terminology

## A
### Active Fields (*Rules Engine*)

These are Database Column names that can be referred to in the body of a Declarative Rule expression. When any code in the system refers to one of these Fields with a read operation an Event (*defined below*) is generated to cause the associated Declarative Rule to be scheduled
for execution.

### Anchor Row ("Rules Engine")

### C
### Catalog

### D
### Declarative Chaining - Forward Chaining (*Rules Engine*)

Declarative Chaining is a contract that defines specific automatic computation or transformation that is guaranteed by the system to occur as Fields, represented by nodes in the network, are modified.

*Forward Chaining* describes the 'automatic' recomputation of the values of fields that have defined declarative relationships. When a Field with a declarative Rule defined on it changes at some point in processing, the value of any dependent Fields are reevaluated based on the declarative Rule's definition.

This means that Forward Chaining can result in an open ended cascade of Rule firing as the output from one declaratively defined Field is updated by the firing of other Field updates.

Forward Chaining performs optimally on Fields that are updated infrequently where the resulting recomputed values are read frequently. Forward Chaining should be used instead of Backward Chaining in these cases.

### Declarative Rules (*Rules Engine*)

Rules that are automatically fired by events when fields are updated. The definition of a declarative Rule does not require a Rule name, but must be annotated as declarative in some way. Upon entry Declarative Rules are parsed to generate the list of Fields that are referenced in the expression. The import and enabling of a Component containing declarative rules causes all referenced fields to be annotated in the schema as 'Active' fields. The field on the right side of the expression is also marked as read-only (for performance sake) since changing it would also immediately queue an event to reset its value.

Declarative Rules should be designed to be as light-weight as possible. Forward Chaining allows breaking up complex relationships into multiple rules.

## E
### Edge (*Database*)

##### Aliases: Link

An edge captures a relationship between two Nodes. The edge metadata captures the type of the nodes that are linked as well as whether the edge is directional or not. An edge may also be thought of as a schematized list of properties.

### Event (*Rules Engine*)

Describes input to the system that Gaia uses to fire a Rule. Events can be generated from incoming sensor data, Database operations (such as a commit), Field updates; pretty much anything that you can attach to a processing action (a Rule). Events are defined at the source of the change (a sensor's input stream, a schema entry) and a type of event. The event also names a Describes input to the system that Gaia uses to fire a Rule. Events can be generated from incoming sensor data, Database operations (such as a commit), Field updates; pretty much anything that you can attach to a processing action (a Rule). Events are defined at the source of the change (a sensor's input stream, a schema entry) and a type of event. The event also names a rRuleule that is to be fired when the specific type of event occurs.
 that is to be fired when the specific type of event occurs.

Ex: Database:Insert, Database:Commit, ML:Identified (a pic), (in the schema)Field x:changed, Sensor Y: Output-Available, etc. Events are managed by a subsystem of the Rules Engine.

### Data Access Classes


## G

### Gaia Declarative Policy Platform

### Gaia Field Pointer (*Database*)

A Gaia Field Pointer provides direct access to a field of a TrueGraphDB entity.

-   Needs discussion about the implementation

### Gaia ID (*Database*)**

A persistent identifier of entities stored in TrueGraphDB (nodes and
edges, not records). Internally, a Gaia ID maps to a locator, to allow
access to its corresponding entity; this mapping is established at
database startup or entity creation.

### Gaia Platform

\[TBD\]

### Gaia Pointer (*Database*)

A Gaia Pointer is an opaque handle that can be used to reference a
TrueGraphDB entity. It is implemented as a ***locator*** reference.

## L
### Locator (Database)

An in-memory entity reference. Internally, a locator is implemented as a *slot id* in a *slot table* that contains memory offsets.

## P
### Policy Set

## R
### Record (Database)

A record is a basic entity container. Unlike a Node, it cannot be referenced by edges or nodes.

### Rule (*Rules Engine*)

Declarative Rules that are executed automatically in response to access or changes to the values of Fields that they contain references to. When Declarative Rules are created, they generate the metadata required to update the schema with the information required to cause the database to take note of when the associated Fields are changed, and provide the name of the defining Declarative Rule that will be triggered  when Fields are accessed.

### Rule Author (*Rules Engine*)

The user who is Authoring Rules to configure the system.

### Ruleset (*Rules Engine*)

This is a logical container for a set of related rules to exist within. Rulesets are named & versioned. These containers will be used for importing and exporting blocks of rules, managing system updates. A Ruleset in an active system can be enabled or disabled; when disabled
none of the rules within are visible or executable.

### Rules Engine (*Rules Engine*)

The subsystem that handles the management, code generation and execution of rules.

