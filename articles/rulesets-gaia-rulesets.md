---
author: Don Glover
owner: Don Glover
lastupdate: 05/28/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Gaia Rulesets

## Overview

Gaia applications are built around sets of rules interacting tightly with a database. The Gaia programming environment enables intermixed declarative and procedural code. There are many approaches to declarative programming, each of which can provide programmer productivity gains. Gaia declarative programming focuses on policy-based applications.

Gaia extends C++ with declarative programming functionality. These declarative extensions couple directly with Gaia's in-memory database, where you can model your application’s state and data. When the data changes, declarative policies are immediately dispatched to trigger high-speed, responsive behavior.

Policies are expressed as rules. In Gaia, Rules react to changes to the database. When the data changes, the Gaia rules engine fires an event, which causes the body of the Rule to fire. 

This allows you to focus on *what* behavior you want; not *how* it gets implemented. This means that you define the logic behind your goals without needing to describe the control flow.

Rules are encapsulated in Rulesets. The Gaia rules engine manages the execution of the Rules across all the Rulesets of your application. It fires rules by following the data change events made to the Gaia database from both other Rules and the procedural code of your application.

## The Declarative System

What makes rules declarative is that the rules engine determines the execution order based on each rule’s definition and its use of the database. Thus, in a declarative system, the programmer is relieved of the need to specify the flow of control between rules manually.

The declarative nature of Gaia makes it possible to write applications with much less code. There are several ways that Gaia makes this possible. Gaia eliminates most of the complex control flow; the platform resolves that for you. Additionally, the declarative system understands the database; this allows Gaia to accomplish the following tasks without you having to provide code:

- Navigation across and between tables is automatic.
- In many cases, accessing sets of rows and changing sets of rows is as simple as it is for single rows.

The entire system depends on the fact that the Catalog fully describes the structure of the active database. The Catalog is the Gaia system component that manages interactions between your code and the database. The names and types of all fields are rowed there. In addition, the relationships between all the rows/tables are in the Catalog too.

The Catalog makes it possible for row navigation to be invisible and automatic in declarative code. When a value in the database changes, Gaia consults the Catalog and fires the rules that are associated with the field. The Rule starts at the correct row, so no code is required to get there. It is the rules engine that decides which Rule to fire, not code that you provide.

When writing your declarative code, keep in mind that each Rule runs within a separate OS thread, and each thread can only have one outstanding transaction at a time. This provides transaction isolation. Two simultaneously running rules (each with a unique thread and transaction) will never see each other's changes. Before the rest of the system can see the actions resulting from the Rule, the Rule must exit to commit the transaction.

To state this another way:

- Rules run on separate threads. When data identified by an Active Field changes, your app can check the database before rules based on the field run. 
=Rules processing is atomic. A Rule must complete execution before you can see the results of actions due to the changes.

