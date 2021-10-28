---
author: Don Glover
owner: 
lastupdate: 10/26/2021
---

# Using the Direct Access Classes

The Direct Access Classes (DAC) provide an API that allows you to work directly with the Gaia database in conjunction with the Declarative C++ rules. Gaiac and gaiat generate the headers and API calls that allow you to access data in a copy-free way, transactional, thread-safe way.

Accessing a value in a database record in Gaia is as fast as accessing a variable; this sets Gaia apart from most other in-memory databases.

This walkthrough models the doctor/patient relationships in a hospital setting.

## Generating Direct Access Classes

You use gaiac to read the schema of a database and generate the Direct Access Classes (DACsho) for the tables contained in it.

The following shows the contents of a DDL file (hospital.ddl) that contains a simple schema for a database that relates patients to their doctor.

```sql
    database hospital

    table doctor (
        name string,
        patients references patient[]
    )

    table patient (
        name string,
        height uint8,
        is_female bool,
        doctor references doctor,
        address references address
    )

    table address (
        street string,
        city string,
        patient references patient 
    )
```

To load the schema contained in the hospital.ddl to the Gaia Catalog and generate the direct access classes, run the following command:

`gaiac hospital.ddl -d hospital -g`

The (-g) option tells gaiac to generate the direct access classes, and the (-d hospital) option specifies which database to use.

If the DDL is already loaded in the Gaia database, we can run the following command. -o hospital tells to generate the header into the hospital folder:

`gaiac -d hospital -g -o hospital` 

This set of options generates a hospital folder with three files:

```
    hospital/
      gaia_hospital.h        
      gaia_hospital.cpp
      hospital_generated.h
```

* gaia_hospital.h: The public header that you include in your code. It defines the direct access API methods that you use.

* gaia_hospital.cpp: The implementation of gaia_hospital.h.

* hospital_generated.h: Included by gaia_hospital.h; contains the serialization logic. Typically you will not need to reference this file directly.

* The filenames are determined by gaiac, and there isn't a provided way to change the names of the output files.

The following is a simple example of direct access API usage that creates a doctor record with the name \"Gregory House\" (hospital.cpp):

```cpp
    #include <gaia/db/db.hpp>
    #include "gaia_hospital.h"

    using namespace gaia::hospital;

    int main()
    {
        gaia::db::begin_session();
        gaia::db::begin_transaction();

        doctor_t::insert_row("Gregory House");

        gaia::db::commit_transaction();
        gaia::db::end_session();
    }

```

Use the following command to manually compile the code:

```
clang++-10 hospital.cpp hospital/gaia_hospital.cpp /usr/local/lib/libgaia.so -I hospital/ -I /opt/gaia/include -lpthread -o hospital_app
```

## CMake helper functions

The Gaia SDK provides CMake functions to facilitate the integration with the Gaia tools.

The function `process_schema`, within the file cmake/gaia.cmake, handles importing a DDL file and generating the Direct Access classes.

```
    project(hospital)

    # We need pthreads support.
    set(CMAKE_THREAD_PREFER_PTHREAD TRUE)
    set(THREADS_PREFER_PTHREAD_FLAG TRUE)
    find_package(Threads REQUIRED)

    # Includes the gaia cmake functions
    include("/opt/gaia/cmake/gaia.cmake")

    # Generate Direct Access classes from DDL
    process_schema(
      DDL_FILE ${PROJECT_SOURCE_DIR}/hospital.ddl
      DATABASE_NAME hospital
    )

    add_executable(hospital
      hospital.cpp
    )

    # Automatically adds "gaia_hospital.cpp" to the gaia_app target 

    target_add_gaia_generated_sources(gaia_app)
    target_include_directories(hospital PRIVATE ${GAIA_INC})
    target_link_libraries(hospital PRIVATE gaia rt Threads::Threads)
    Direct Access Classes
```