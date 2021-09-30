---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

# About the Gaia Platform

The information contained in this document represents information about prerelease features of the product. Features might change when the product is released for general availability.

# Overview

Gaia Platform is a software development framework that makes it easier to program autonomous systems at the edge. Gaia empowers programmers to make use of low-code features while still being appropriate for industrial use cases. Combining a high-speed in memory database with Declarative C++ language extensions, Gaia proviDes a performant and intuitive model for development. Model your state in the database, and when things change, your declarative application logic will respond automatically.

As a developer, you can focus on what your program needs to do and let Gaia work out how it gets done. With Gaia, you'll write less code that's easier to read and more intuitive to debug and test. You can integrate multiple system components in a common setting with less boilerplate, from machine learning functions to ROS-enabled actuators. And you can run it all without the need for constant cloud connectivity.

In a phrase, Gaia Platform is the industrial-strength low-code development environment for apps at the edge.

## Why Gaia?

Writing software is complicated. As you look to develop autonomous systems that need to operate reliably and intelligently out in the real world, you can find that the difficulty has only increased. There's a lot to get working and a lot of moving pieces to integrate. For example, machine learning has come a long way, but it takes more than obstacle detection to build an interesting product. Plus, these systems generate a ton of data we need to deal with, which results in lots of code that's hard to debug and even harder to maintain over its lifetime.

Gaia exists to overcome these challenges. The Gaia Platform is data-centric and responsive, like the world of autonomous machines. It is the nexus for integrating all the distributed components comprising your autonomous system and orchestrating their behavior to deliver on your mission's objectives.

Are you looking for a way to accelerate the development of a new IoT prototype? Do you have subject matter experts with lots of knowledge and limited coding experience? What about a complex robotics product with an unwieldy state-machine?

## How can you use Gaia?

The Gaia Platform supports numerous application scenarios with a common theme of operating in complex scenarios at the edge. Gaia's database and policies can be used to inform the behavior of an application directly. Write policies to respond to specific scenarios as informed by data (state) in the Gaia database.

For example, you're building an Autonomous Guided Vehicle (AGV) to move material around a warehouse. You can use Gaia's database to model inventory and waypoint data so that your AGV can work without a direct connection to the internet. Throughout its mission, your AGV might generate lots of interesting and frankly uninteresting data. Use Gaia's DB to store it for the moment and implement data fusion and filtering logic with Gaia policies. You can write a policy that throws away intermediate navigation data but retains any discoveries about the location of inventory. At the end of the mission, your AGV can sync back only what's critical so that you can save on cloud bandwidth and storage costs.

In systems in which pair Machine Learning (ML) and Deep Learning (DL) with perception and action planning methods, the Gaia Platform provides robust rules handling and event messaging to understand the data.

What does this mean for Autonomous Systems? Autonomous Systems typically engage in a process of *perceiving* their environment, *understanding* an operational context, and *acting* accordingly. ML/DL is excellent at turning data into meaningful semantics, such as examining an RBG tensor and producing a label like "kitty!". To go from "I see the kitty" to "I will pet the kitty" requires additional layers of software to take the data from the perception engine and contextualize its output to make the decision. This is where Gaia comes in.

Gaia allows you to define all the logic that contextualizes these inputs, understand the situation, and translate it into a decision. Based on the decision, Gaia can raise an event that sends an instruction to the robotic arm to pet the kitty.

Beyond applications in Autonomous Systems, Gaia provides an Industrial strength transactional store that supports a transactionally safe framework. Whatever happens, you will always have a consistent database from which you can share data and act on it robustly.

## How it works

Gaia is a platform in the sense that users build their applications on top of its functionality. Gaia runs on the Linux operating system and supports C++ (and in the future Python) programming language with declarative functionality. Gaia's in-memory database is installed with the platform and enables seamless integration between database operations and application control code.

The Gaia Platform consists of the following elements:

-   A shared in memory database
-   An events engine
-   A Catalog that interfaces with your app, the events engine, and the database

And provides the following tools:

-   Gaia Catalog compiler. The catalog compiler creates the datastore and tables that support your application. It also translates Gaia DDL files into the Gaia headers you include in your ruleset and     app code files. For more information, see [Gaiac and DDL](articles/reference/ddl-gaia.md).
-   Gaia Translation Engine. The translation engine converts your rulesets into code files that you include in your app.

These components and tools interact to create an end-to-end solution. To accomplish this, Gaia addresses the PUA paradigm: Perceive, Understand, and Act. In Gaia, this is expressed as:

-   Perceive - Where inputs come from
-   Understand - Gaia platform
-   Act - Rules engine policies/ integration with devices

### Perceive

Your procedural code gathers the data from the inputs and writes them the Gaia database. Gaia itself is not focused on direct control of the actions you take to control and read devices such as microcontrollers.

This includes:

-   Interrupt Processing
-   Device Drivers
-   Fine-grained manipulation of actuators
-   Etc.

The Database is Active. This means that it functions as an Event system that monitors the incoming changes to the database and publishes them for subscribed rules.

The Gaia platform implements the database as an in memory high-performance data store.

For more information about defining your Gaia database, see [Gaiac and DDL](articles/reference/ddl-gaia.md).

### Understand

The primary focus of rules is the Policy layer of applications. You define rules using easy-to-read and understand declarative code. The declarative code that defines a Rule takes the form of "If this happens, then do that."

The rules engine and database work together to facilitate:

-   Adaptive responses and process diversion resolution. For example, handle route diversion when a drone air taxi encounters bad weather.
-   Data-driven decision-making. For example, Is this person allowed to be in that room at this hour.

If you find edge cases where you need just a bit more control, you can intermix declarative code (rules) with procedural code.

For more information about defining your rules, see [Gaia Rulesets](articles/rulesets-gaia-rulesets.md).

### Act

Your code acts on the data and decisions produced by your rules. Gaia integrates with other platforms such as ROS2, including lower-level motion planning systems and other features to interact with your hardware. With Gaia, you can send a high-level instruction "go to this waypoint" to a ROS2 node that handles the task of navigating to that location.

## Summary

Gaia provides a catalog that binds the database to the Gaia rules engine. Rules are written in declarative code and enable event-driven processing. The declarative code binds the rules to changes in the database without complex control flow logic.

## Next Steps

* [Get started with the Gaia Platform](articles/getting-started-with-gaia.md)
* [Write your first Gaia App](articles/tutorials/writing-first-gaia-application.md)
* Learn more about [Gaia Rulesets](articles/rulesets-gaia-rulesets.md)
