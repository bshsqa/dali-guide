---
id: scripting
title: "scripting"
sidebar_label: "scripting"
---
## Introduction to the DALi Scripting Module

The DALi Scripting module provides a metadata-driven framework for bridging the gap between serialized data formats (like JSON or property maps) and the engine's internal C++ [object](./object.md) structures. It is specifically designed to facilitate dynamic [object](./object.md) initialization, configuration parsing, and bi-directional conversion between human-readable strings and engine-specific enumerations.

Developers should utilize this module when building systems that require UI definition via external data files, such as dynamic layout loaders or theme engines. It distinguishes itself by providing a robust, type-safe infrastructure for property mapping, ensuring that the transition from configuration-driven inputs to C++ logic remains performant and maintainable.

## Internal Architecture and Data Flow

The [scripting](./scripting.md) subsystem acts as an integration layer that interprets `Property::Map` structures to construct or configure DALi objects. By leveraging static metadata tables, the engine can deserialize complex [object](./object.md) graphs without hardcoding every property assignment in the application layer.

### Object Construction via Property Maps
The module provides functions to instantiate complex DALi objects directly from serialized property maps. These functions map input keys to internal setter logic, enabling high-level UI descriptions to trigger low-level engine initialization.

**API: `Dali::Scripting::NewActor`**
*   **WHAT:** Creates an actor instance initialized with properties defined in the provided map.
*   **WHY:** Use this when constructing node hierarchies from external data sources or dynamic UI files.
*   **HOW:** Takes a `const Property::Map &map` containing property name/value pairs. Returns the initialized `Actor`.

**API: `Dali::Scripting::NewAnimation`**
*   **WHAT:** Populates an `AnimationData` [object](./object.md) using parameters defined in a property map.
*   **WHY:** Facilitates the creation of animations defined in external configuration files rather than hardcoded C++.
*   **HOW:** Takes a `const Property::Map &map` and a `Dali::AnimationData &outputAnimationData` reference to store the results.

```cpp
#include <dali/scripting/scripting.h>

void CreateDynamicUI(const Dali::Property::Map& map) {
    // Create an actor from a property map defining its properties
    Dali::Actor myActor = Dali::Scripting::NewActor(map);
    Dali::Stage::GetCurrent().Add(myActor);
}
```

## Enum Serialization with StringEnum

The `StringEnum` structure is the foundation of the scripting module's ability to translate between human-readable configuration strings and machine-efficient integer enumerations.

### Using StringEnum
A `StringEnum` instance pairs a constant string identifier with its corresponding integer enum value. These are typically organized into arrays that act as lookup tables for the `Scripting` namespace utilities.

```cpp
const Dali::Scripting::StringEnum myEnumTable[] = {
    { "ValueOne", 1 },
    { "ValueTwo", 2 }
};
```

**API: `Dali::Scripting::GetEnumeration`**
*   **WHAT:** Converts a configuration string to its associated enum value using a provided lookup table.
*   **WHY:** Allows user-facing strings to be safely resolved to C++ enum types during property parsing.
*   **HOW:** Takes `const char *value`, the `StringEnum *table`, `uint32_t tableCount`, and a reference `T &result`. Returns `true` if found, `false` otherwise.

```cpp
int myEnumResult;
const char* input = "ValueTwo";
if (Dali::Scripting::GetEnumeration(input, myEnumTable, 2, myEnumResult)) {
    // myEnumResult is now 2
}
```

## Implementing Property Mapping using Enum Helpers

The `enum-helper.h` file provides a suite of preprocessor macros designed to simplify the creation of these lookup tables, reducing boilerplate and minimizing errors when defining mappings.

### Defining Mappings
Use the `DALI_ENUM_TO_STRING_TABLE` macros to define a mapping table. This allows the scripting system to perform automatic serialization and deserialization for your custom types.

> Note: Always ensure the `tableCount` provided to `Scripting` functions matches the actual number of entries in your array to avoid buffer overruns.

```cpp
#include <dali/scripting/enum-helper.h>

DALI_ENUM_TO_STRING_TABLE_BEGIN(MyCustomEnum)
  DALI_ENUM_TO_STRING(ValueOne)
  DALI_ENUM_TO_STRING(ValueTwo)
DALI_ENUM_TO_STRING_TABLE_END(MyCustomEnum)
```

**API: `Dali::Scripting::GetEnumerationName`**
*   **WHAT:** Retrieves the string representation for a given enumeration value.
*   **WHY:** Useful for debugging or serializing the current state of an object back into a string format.
*   **HOW:** Accepts the enum value `T`, the table, and the table count. Returns a `const char*`.

## Thread Safety and Lifecycle Considerations

The scripting subsystem is primarily designed for use on the Main (Update) Thread of the DALi engine. Since scripting tables are often defined as static constants, they are inherently thread-safe for reading; however, any object manipulation resulting from scripting calls must adhere to DALi's thread-affinity rules for the scene graph.

*   **Static Metadata Persistence:** Ensure that your `StringEnum` tables are defined in a scope that survives for the duration of the object's lifecycle.
*   **Re-entrancy:** While lookup functions are read-only and re-entrant, modifying an actor's state during an animation construction loop from the scripting layer may trigger scene graph updates that must be synchronized with the engine's frame cycle.

## Integration API Usage and Best Practices

When extending the engine using the integration-api, developers should register custom properties to allow the scripting module to interact with new components seamlessly.

### Registering Custom Scriptable Properties
When defining a new component, use `GetEnumerationProperty` or `GetBitmaskEnumerationProperty` within your custom `SetProperty` implementation to handle incoming data from the scripting system.

**API: `Dali::Scripting::GetEnumerationProperty`**
*   **WHAT:** Safely extracts an enumeration from a `Property::Value`, which could be an integer or a string.
*   **WHY:** Provides a unified way to handle properties that accept both raw IDs and descriptive strings.
*   **HOW:** Takes `const Property::Value &propertyValue`, the `StringEnum *table`, `uint32_t tableCount`, and reference `T &result`.

```cpp
void MyComponent::SetProperty(Property::Index index, const Property::Value& value) {
    if (index == MY_ENUM_PROPERTY) {
        MyEnum result;
        if (Dali::Scripting::GetEnumerationProperty(value, myEnumTable, 2, result)) {
            // Apply result to component
        }
    }
}
```

> Warning: Always validate that the provided `Property::Value` matches the expected type before calling `GetEnumerationProperty` to prevent engine-level assertions or crashes.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/scripting)
