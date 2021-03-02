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

### C
### Catalog

### D
### Declarative Chaining - Forward Chaining (*Rules Engine*)

Declarative Chaining is a contract that defines specific automatic computation or transformation that is guaranteed by the system to occur as Fields, represented by nodes in the network, are modified.

***Forward Chaining*** describes the 'automatic' recomputation of the values of fields that have defined declarative relationships. When a Field with a declarative rule defined on it changes, at some point in processing***\*1***, the value of any dependent Fields are reevaluated based on the declarative rule's definition.

This means that Forward Chaining can result in an open ended cascade of rule firing as the output from one declaratively defined Field is updated by the firing of other Field updates.

Forward Chaining performs optimally on Fields that are updated infrequently where the resulting recomputed values are read frequently. Forward Chaining should be used instead of Backward Chaining in these cases.

### Declarative Rules (*Rules Engine*)

Rules that are automatically fired by events when fields are updated. The definition of a declarative rule does not require a rule name, but must be annotated as declarative in some way. Upon entry Declarative Rules are parsed to generate the list of Fields that are referenced in the expression. The import and enabling of a Component containing declarative rules causes all referenced fields to be annotated in the schema as 'Active' fields. The field on the right side of the expression is also marked as read-only (for performance sake) since changing it would also immediately queue an event to reset its value.

Declarative Rules should be designed to be as light-weight as possible. Forward Chaining allows breaking up complex relationships into multiple rules.

## E
### Edge (*Database*)

##### Aliases: Link

An edge captures a relationship between two Nodes. The edge metadata captures the type of the nodes that are linked as well as whether the edge is directional or not. An edge may also be thought of as a schematized list of properties.

### Event (*Rules Engine*)

Describes *<uany</u* input to the system that we wish to use to trigger some form of processing, usually rules. Events can be generated from incoming sensor data, Database operations (like a commit), Field updates, pretty much anything we wish to attach to a processing action (a rule). Events are defined at the source of the change (a sensor's input stream, a schema entry) and a type of event. The event also names a rule that is to be fired when the specific type of event occurs.

Ex: Database:Insert, Database:Commit, ML:Identified (a pic), (in the schema)Field x:changed, Sensor Y: Output-Available, etc. Events are managed by a subsystem of the Rules Engine.

### Extended Data Classes


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

### Gaia Platform**

\[TBD\]

Usage: Proper capitalization? Gaia Platform vs Gaia platform.

### Gaia Pointer (*Database*)**

A Gaia Pointer is an opaque handle that can be used to reference a
TrueGraphDB entity. It is implemented as a ***locator*** reference.

-   Needs discussion about the implementation

Usage: Both words capitalized.

## L
### Locator (Database)**

An in-memory entity reference. Internally, a locator is implemented as a *slot id* in a *slot table* that contains memory offsets.

## N
### Node (*Database*)

A node is an entity container that can be involved in relationships represented by edges. A node may also be thought of as a schematized list of properties. Every node can be identified by its Gaia ID. Nodes could directly reference other nodes, for example, to form various data structures.

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

### TrueGraphDB (*Database*)

An in-memory, relational, graph-oriented, object database that also provides direct access to its entities/objects, which are nodes and edges of various types. Each entity can be seen as a record in the table represented by the collection of entities of the same type.
