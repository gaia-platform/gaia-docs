---
author: 
owner: 
lastupdate: 
---

# Rules development example

---

**NOTE**

The information contained in this document represents information about prerelease features of the product. Features might change when the product is released for general availability.

---

This document describes the creation of rules for a sample pallet tracking system.

You define rules to respond whenever data is changed is committed to the database, whether that is at the row level, or an individual field.

## Prerequisites

-   A basic understanding of the Gaia database. For more information,
    see [Gaiac and DDL](gaiac-ddl.md).
-   A basic understanding of rules and their creation. For more
    information, see [Rules creation basics](rules-creation-basics.md).

In the design phase for your system, you define the business logic that describes how your system operates. After your initial design is complete, the architecture of the Gaia Platform lends itself best to the following workflow:

-   Create a schema for your application.
    -   The schema defines tables and fields that contain the state of your system at any given time.
    -   Add tables to manage the state of your application.
    -   Determine which columns in the schema drive the behavior. Your rules will act on these fields.
-   In your makefile run gaiac to import the schema and create tables for your database. The Catalog provides a key resource in documenting and understanding systems because it contains metadata that describes the Data and Rule connections and interactions.
    -   Gaiac generates a header that contains the edc classes that define your schema in code. You include in this header your ruleset definition file.
-   Create your ruleset.
    -   Identify the actions to be performed when an Active Field changes.
-   Run gaiat to translate the ruleset into code files that you include in your app.
-   Create your application and call the code supplied by gaiat.

 Build the data model, then add Rules to handle the required processing. Add event handlers to connect external sensors and actuators to your model and you have a system. System, unit and integration testing is simplified with our "Data at the center" model; it allows test code to insert simulated events into the system and verify the correctness of the responses.

## **Business logic**

For the sample application the business logic is shown in this diagram:

![Example rules process flow](images\rules-example-process-flow.png)

The camera sees a new code

Determine what the code is:

-   Loading Area
-   Pallet
-   Package
-   Unknown

## Database schema

To support the initial definition of the business logic, we define our database schema as follows:

```
facility

id : string
loading\area
id : string,
last\pallet\id : string active,
current\pallet\id : string active,
current\package\id : string active,
loading\area\\facility references facility

camera

id : string,
function\code : string,
camera\data\code : string active,
camera\\loading\area references loading\area

pallet

id : string,
manifest\id : string active,
pallet\\loading\area references loading\area

manifest

id : string,
state : string active,
desired\qty : int32,
actual\qty : int32 active,
manifest\\pallet references pallet

manifest\package

id : string,
package\id : string,
desired\qty : int32,
actual\qty : int32 active,
manifest\package\\manifest references manifest

package

id : string,
package\\manifest references manifest
```

## Rules Definitions

The Rules of the system are defined directly on data, at the active field or table level. The Rules are invoked automatically as that data is added or modified within the system.

The example below models the example rules process flow of the design.

```cpp
std::string data = "";

// Here we define our declarative rules
//
ruleset smartpallet_ruleset
{
    {
        // The camera sees a new code
        //
        // Reacts to: camera.camera_data_code
        // Changes:
        //      loading_area.last_pallet_id
        //      loading_area.current_pallet_id
        //      loading_area.current_package_id
        //      camera.camera_data_code
        //
        if(strlen(camera.camera_data_code) > 0)
        {
            send_status_message("DEBUG",
                std::string("Code Seen : ") +
                camera.camera_data_code, "NONE");

            switch( get_object_type(camera.camera_data_code) )
            {
                    case object_type_enum::loading_area_type:
                        data = std::string(@loading_area.current_pallet_id);
                        loading_area.last_pallet_id = @loading_area.current_pallet_id;
                        loading_area.current_pallet_id = "";
                    break;
                    case object_type_enum::package_type:
                        loading_area.current_package_id = camera.camera_data_code;
                    break;
                    case object_type_enum::pallet_type:
                        loading_area.current_pallet_id = camera.camera_data_code;
                    break;
                    default:
                        send_status_message("ERROR",
                            std::string("Unidentified Object Seen : ") +
                            camera.camera_data_code, "RED");
                    break;
            }

            //Reset
            camera.camera_data_code = "";
        }
    }
    {
        // A pallet has moved onto or off of a loading area
        //
        // Reacts to: loading_area.current_pallet_id
        // Changes:
        // Creates: pallet
        //
        if(strlen(loading_area.current_pallet_id) == 0)
        {
            send_status_message("DEBUG",
                std::string("Loading area : ") +
                @loading_area.id + " is empty", "NONE");
        }
        else
        {
            send_status_message("DEBUG",
                std::string("Pallet: ") +
                loading_area.current_pallet_id +
                " in loading area: " +
                @loading_area.id, "NONE");

            auto cbc = ismartpallet::get_callback_class();

            // find the next new manifest
            auto manifest_id = cbc->cb_get_next_manifest_id();

            if( manifest_id.length() == 0 )
            {
                send_status_message("OPS",
                    std::string("No manifests pending. Do not load pallet."), "RED");
                return;
            }

            // insert a pallet row into the db
            cbc->cb_insert_pallet(@loading_area.current_pallet_id, @loading_area.id, manifest_id);

            send_status_message("DEBUG",
                std::string("Manifest: ") +
                manifest_id + " added to pallet: " +
                loading_area.current_pallet_id, "NONE");

            send_status_message("OPS",
                std::string("Pallet ready for loading"), "YELLOW");
        }
    }
    {
        // if last_pallet_id changed
        //
        // Reacts to: loading_area.last_pallet_id
        // Changes:
        //
        auto cbc = ismartpallet::get_callback_class();

        // find the pallet manifest
        auto manifest_id = cbc->cb_get_pallet_manifest_id(loading_area.last_pallet_id);

        if(cbc->cb_get_manifest_state(manifest_id) == "FULL")
            send_status_message("OPS",
                std::string("Pallet: ") +
                loading_area.last_pallet_id +
                " removed from loading area: " + @loading_area.id, "GREEN");
        else
            send_status_message("OPS",
                std::string("Pallet: ") +
                loading_area.last_pallet_id +
                " removed from loading area: " + @loading_area.id +
                " was not full, recall pallet immidiately.", "RED");
    }
    {
        // A package has moved onto a loading area
        //
        // Reacts to: loading_area.current_package_id
        // Changes: loading_area.current_package_id
        // Creates: package
        //
        if(strlen(loading_area.current_package_id) > 0)
        {
            //send_status_message("DEBUG",
            //    std::string("Package: ") +
            //    loading_area.current_package_id +
            //    " in loading area: " +
            //    @loading_area.id, "NONE");

            auto cbc = ismartpallet::get_callback_class();

            // find the pallet manifest
            auto manifest_id = cbc->cb_get_pallet_manifest_id(@loading_area.current_pallet_id);

            // find the pallet manifest package
            auto manifest_package_id = cbc->cb_get_manifest_package_id(manifest_id, @loading_area.current_package_id);

            // if we didnt find this line item in this manifest
            if(manifest_package_id.length() == 0)
            {
                send_status_message("OPS",
                    std::string("Package: ") +
                    loading_area.current_package_id +
                    " is not a line item on this manifest. Remove immidately.", "RED");

                return;
            }

            // add package to db
            cbc->cb_insert_seen_package(loading_area.current_package_id, manifest_id);

            send_status_message("DEBUG",
                std::string("Package: ") +
                loading_area.current_package_id +
                " in loading area: " + @loading_area.id +
                " added to manifest: " + manifest_id, "NONE");

            //Reset
            loading_area.current_package_id = "";
        }
    }
    {
        // A package has been added to a manifest
        //
        // Reacts to: package.LastOperation
        // Changes: manifest.actual_qty
        //
        if(package.LastOperation == INSERT)
        {
            if( @manifest.actual_qty + 1 > @manifest.desired_qty )
                send_status_message("OPS",
                    std::string("Too many packages on manifest: ") +
                    @manifest.id, "RED");
            else
            {
                send_status_message("OPS",
                    std::string("Package : ") + @package.id + " added to manifest: " +
                    @manifest.id, "YELLOW");

                manifest.actual_qty += 1;
            }
        }
    }
    {
        // Check if manifest is full
        //
        // Reacts to: manifest.actual_qty
        // Changes: manifest.state
        //
        if( manifest.actual_qty == @manifest.desired_qty )
        {
            send_status_message("OPS",
                std::string("Manifest : ") + @manifest.id +
                " is complete with : " + std::to_string(@manifest.desired_qty) + " packages", "GREEN");

            manifest.state = "FULL";
        }
        else
        {
            send_status_message("OPS",
                std::string("Manifest : ") + @manifest.id +
                " has " + std::to_string(@manifest.actual_qty) +
                " of " + std::to_string(@manifest.desired_qty) + " packages", "YELLOW");
        }
    }
    {
        // Check if manifest line item is over full
        //
        // Reacts to: manifest_package.actual_qty
        // Changes:
        //
        if( manifest_package.actual_qty > @manifest_package.desired_qty )
        {
            send_status_message("OPS",
                std::string("Too many packages of type : ") + @manifest_package.package_id +
                " on pallet. Remove package immediately.", "RED");
        }
    }
}
```

### Rule Chaining

The following figure presents the path of chaining implemented in the rules. Each of the rules presented in the section above has one or more inputs, outputs, and/or creates. Outputs and creates from one rule triggers the activation of rules which have inputs that match the outputs and creates.

Note that the complexity of the relationships may be more complex than shown in the chaining table, because rules may use non triggering objects in addition to triggering objects to change system state.

![](images\rules-example-process-flow.png)

##### Translated C++ Code

The translation process renders each rule as two C++ functions:

1.  A function that is the body of the Rule rewritten with 100% C++.
2.  An additional set of C++ statements that are a contribution to the "well known" initialization function that subscribes this Rule’s  new function to the Event associated with the active fields that are referenced.


 Next question is how does this get executed? Recall that I said this was an active field defined in the schema. That active designation is very important. Writing a Rule that reads an active field causes that Rule to be subscribed to all changes to that field. Whenever this active field is updated and committed in the greeting table, this specific rule is executed automatically. When the Rule is executed, the row that was operated on iswill be delivered to the rule as an automatic parameter. In our example the greetings table is automatically selected based on the fact that the field name my_name is unique and only appears in that table. The my_name reference then is literally referring to the my_name field in the row that was inserted into the greeting table. Since the entire row was passed into the rule, all defined fields from that row are available for use in this rule.

