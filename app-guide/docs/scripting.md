---
id: scripting
title: "scripting"
sidebar_label: "scripting"
---
## Introduction to Scripting

The Scripting module in DALi provides a specialized set of utilities designed to bridge the gap between human-readable strings and internal framework enumerations or data types. It acts as a translation layer, enabling developers to define application properties, styles, or [animation](./animation.md) states using configuration files (like JSON or XML) without requiring direct C++ code changes for every parameter adjustment.

You should use the Scripting module when your application requires data-driven [layouts](./layouts.md) or dynamic property configuration, such as loading actor styles from external resources. It is distinct from standard DALi property systems because it focuses specifically on the conversion logic between symbolic string identifiers and strongly-typed DALi enums, making your code significantly more readable and maintainable when handling high volumes of property updates.

## Using StringEnum for Property Mapping

The `StringEnum` class provides a structured mechanism to map string-based keys to integer-based enumerations used throughout the DALi framework. By centralizing these mappings, you can cleanly parse configuration data into valid property values without excessive conditional logic.

### Mapping Strings to Enums
The `StringEnum` class is a [utility](./utility.md) wrapper that allows you to register string-to-integer pairs and perform bidirectional lookups.

* **WHAT:** This class facilitates the translation of descriptive string identifiers into their corresponding DALi property enumerations.
* **WHY:** It is essential for deserializing configuration files where properties are stored as strings but must be applied to DALi objects as typed integers.
* **HOW:** Use `Add()` to register mappings, and `Find()` or `GetValue()` to retrieve the integer value based on a string name.

> Note: If a string lookup fails, the [utility](./utility.md) typically returns a default or error indicator defined by the specific enumeration context.

```cpp
#include <dali/public-api/dali-core.h>
#include <dali/public-api/scripting/script-bridge.h>

// Example: Mapping string names to DALI::Actor::Property enumerations
const Dali::Scripting::StringEnum PROPERTY_TABLE[] = {
  { "parentOrigin", Dali::Actor::Property::PARENT_ORIGIN },
  { "anchorPoint",  Dali::Actor::Property::ANCHOR_POINT },
  { "position",     Dali::Actor::Property::POSITION }
};

void ApplyProperty(Dali::Actor actor, const std::string& name) {
  int propertyIndex;
  if (Dali::Scripting::GetEnumeration(name.c_str(), PROPERTY_TABLE, 3, propertyIndex)) {
    // Successfully mapped string to property index
    actor.SetProperty(propertyIndex, Dali::Vector3::ZERO);
  }
}
```

## Scripting Helper Functions

The `Scripting` namespace contains static utility functions that handle the conversion of complex types from data sources. These helpers are specifically optimized for DALi's property system, ensuring that external values are correctly cast into types such as `Vector3`, `Vector4`, or `Quaternion`.

### Parsing and Converting Data
These helpers simplify the process of taking a raw string or property value and converting it into a format that the actor system understands.

* **WHAT:** These functions parse input strings or data blocks into specific DALi types.
* **WHY:** They allow developers to avoid manual parsing of strings (e.g., "1.0, 0.0, 0.0") into numerical vectors, reducing boilerplate code.
* **HOW:** Pass the raw input string or source value to the corresponding helper function; the result is returned as the appropriate DALi object or boolean success indicator.

```cpp
#include <dali/public-api/dali-core.h>
#include <dali/public-api/scripting/script-bridge.h>

void ConfigureActorFromScript(Dali::Actor actor, const std::string& positionString) {
  Dali::Vector3 position;
  
  // Convert a string like "100.0, 200.0, 0.0" into a Vector3 object
  if (Dali::Scripting::ConvertVector3(positionString.c_str(), position)) {
    actor.SetProperty(Dali::Actor::Property::POSITION, position);
  }
}
```

## Common Integration Patterns

When building data-driven UIs, scripting utilities are best used at the boundary of your data loading layer. By separating your UI definition from your logic, you create a more flexible application architecture.

### Dynamic Property Assignment
The most common pattern is creating a "Style Manager" or "Loader" class that iterates through a data object (such as a JSON object) and uses Scripting helpers to apply those values to actors on the fly.

* **Best Practice:** Always validate the string input before attempting a conversion to prevent unnecessary framework overhead.
* **Pattern:** Use a `std::map` or similar lookup structure to hold your `StringEnum` arrays, allowing your system to scale as you add more configurable properties.

> Warning: Performance may degrade if intensive string-to-enum lookups are performed every frame. Cache converted properties in local variables rather than re-parsing configuration strings inside an animation or update callback.

→ See: [Dali::Property](https://developer.tizen.org/dev-guide/latest/org.tizen.native.appprogramming/html/guide/ui/properties.htm) (for a detailed overview of the underlying DALi property system).

```cpp
// Example of integrating scripting with dynamic configuration
void LoadActorStyle(Dali::Actor actor, const std::string& key, const std::string& value) {
  // Check against our property table
  int propertyIndex;
  if (Dali::Scripting::GetEnumeration(key.c_str(), PROPERTY_TABLE, 3, propertyIndex)) {
    // Based on propertyIndex, choose the correct conversion helper
    if (propertyIndex == Dali::Actor::Property::POSITION) {
      Dali::Vector3 vec;
      if (Dali::Scripting::ConvertVector3(value.c_str(), vec)) {
        actor.SetProperty(propertyIndex, vec);
      }
    }
  }
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/scripting)
