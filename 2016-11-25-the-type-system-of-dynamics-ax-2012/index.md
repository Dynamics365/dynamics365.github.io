# The Type system of Dynamics AX 2012


The Microsoft Dynamics AX runtime manages the storage of *value type* data on the **call stack** and *reference type objects* on the **memory heap**. 

**The call stack** is the memory structure that holds data about the active methods called during program execution. **The memory heap** is the memory area that allocates storage for objects that are destroyed automatically by the Microsoft Dynamics AX run-time.

## Value types

Value types include the built-in `primitive types, extended data types, enumeration types, and built-in collection types`.

* **The primitive types** are boolean, int, int64, real, date, utcDateTime, timeofday, str, and guid. 
* **The extended data types** are specialized primitive types and specialized base enumerations. 
* **The enumeration types** are base enumerations and extended data types. 
* **The collection types** are the built-in array and container types. 

<!--more-->
>By default, variables declared as **value types** are assigned their zero value by the Microsoft Dynamics AX runtime. These variables can’t be set to null. Variable values are copied when variables are used to invoke methods and when they are used in assignment statements. Therefore, two value type variables can’t reference the same value.

![2015-10-06-the-type-system-of-dynamics-ax-2012](/imagesposts/2015-10-06-the-type-system-of-dynamics-ax-2012.png)

## Reference types

Reference types include `the record types, class types, and interface types`.


* **The record types** are table, map, and view. User-defined record types are dynamically composed from application model layers. Microsoft Dynamics AX runtime record types are exposed in the system application programming interface (API). Although the methods are not visible in the AOT, all record types implement the methods that are members of the system xRecord type, a Microsoft Dynamics AX runtime class type. 
* **User-defined class types** are dynamically composed from application model layers and Microsoft Dynamics AX runtime class types exposed in the system API. 
* **Interface types** are type specifications and can’t be instantiated in the Microsoft Dynamics AX  runtime. Class types can, however, implement interfaces. Variables declared as reference types contain references to objects that the Microsoft Dynamics AX runtime instantiates from dynamically composed types defined in the application model layering system and from types exposed in the system API. The Microsoft Dynamics AX runtime also performs memory deallocation (garbage collection) for these objects when they are no longer referenced.

>Reference variables declared as record types reference objects that the Microsoft Dynamics AX runtime instantiates automatically. Class type objects are programmatically instantiated using the new operator. Copies of object references are passed as reference parameters in method calls and are assigned to reference variables, so two variables can reference the same object.  

Thank you for reading!

