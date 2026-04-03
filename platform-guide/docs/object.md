---
id: object
title: "object"
sidebar_label: "object"
---
## Introduction to the DALi Object Framework

The DALi Object module serves as the foundational architectural layer for the entire engine, providing the mechanisms for type identification, property-based messaging, and [object](./object.md) lifecycle management. It enables a unified interaction model where developers can manipulate engine entities through a standardized interface, abstracting the underlying complexity of the C++ implementation.

You should use this framework when creating custom engine components that require integration with DALi's [animation](./animation.md), property, or signal systems. What makes it distinct is its deep integration with the engine's core, allowing for late-bound [object](./object.md) behavior and efficient cross-language data handling.

## Object Lifecycle and Memory Management

DALi utilizes a reference-counted memory model based on `RefObject` and handled via the `BaseHandle`/`Handle` paradigm. This ensures that objects remain in memory as long as there is at least one active handle referencing them, automatically triggering destruction when the last reference is released.

### Handle-Based Lifetime
Handles provide a smart-pointer-like interface to the underlying C++ objects. When you create an [object](./object.md), you receive a handle that manages its lifetime, preventing memory leaks while allowing for safe, multi-threaded access within the engine.

> Note: Never delete a DALi [object](./object.md) manually; instead, reset its handle or allow the handle to go out of scope.

## Type Registration and Reflection Architecture

The Type Registry is the engine's central repository for [object](./object.md) metadata, allowing the system to query capabilities and map property names to internal logic at runtime. By registering classes with the `TypeRegistry`, objects become discoverable by the DALi serialization and [animation](./animation.md) systems.

## The Property System: Structure and Storage

The Property system provides a dynamic key-value storage architecture that allows developers to get or set state on objects without knowing the underlying class implementation. Properties are stored within `Property::Map` or `Property::Array` structures, using union-based storage to handle various data types efficiently.

## Dynamic Property Registration and Linking

Integration APIs allow for the extension of base objects with custom properties at runtime. This allows developers to hook into the engine's property [animation](./animation.md) system even for non-standard, user-defined data fields on an `Actor`.

## Property Notifications and Conditionals

The notification system allows you to observe property changes and execute specific logic or triggers when a value crosses a threshold. This is critical for driving UI state changes or complex animations based on [object](./object.md) movement or state transitions.

## Type-Safe Data Handling with Any

The `Dali::Any` class is a type-erased container used throughout the DALi APIs to pass arbitrary data types while maintaining type safety. It enables flexible communication between disparate engine modules by wrapping data in a polymorphic structure that can be safely cast back to its original type.

## Interfacing with the C# Bridge

DALi provides a dedicated C# interop layer that bridges the gap between C++ [object](./object.md) instances and the managed runtime. This allows developers to expose native DALi objects to C# applications, maintaining the same lifecycle and property-access patterns across language boundaries.

## Observer Patterns and Event Handling

The `BaseObjectObserver` provides an interface to monitor internal state changes and lifecycle [events](./events.md). By implementing this observer, you can track the destruction or modification of objects, ensuring that dependent systems stay synchronized with the engine's internal state.

### Using Actor as a Base Object
While the `Object` framework is abstract, the `Dali::Actor` is the primary concrete realization developers interact with. It manages the hierarchy and spatial representation of objects within the scene graph.

```cpp
#include <dali/dali.h>

void CreateAndManageActor()
{
  // Create a new Actor instance
  Dali::Actor myActor = Dali::Actor::New();

  // Add the actor to a hierarchy (e.g., as a child of another actor)
  Dali::Actor rootActor = Dali::Actor::New();
  rootActor.Add(myActor);

  // Retrieve information about the object
  uint32_t childCount = rootActor.GetChildCount();
  
  // Example of using handles: copying a handle does not copy the object, 
  // it creates a new reference.
  Dali::Actor actorReference = myActor;
  
  // Remove from parent to decrease reference count if no other handles exist
  myActor.Unparent();
}
```

> Warning: Always check if an `Actor` handle is initialized using the `bool` operator or `!operator` before accessing its methods, as uninitialized handles will throw an assertion error in debug builds.

### Spatial Manipulation and Hierarchy
Actors use the `Object` framework to support transformation propagation throughout the scene graph.

```cpp
void TransformActor(Dali::Actor& actor)
{
  // Apply a relative translation to the actor
  actor.TranslateBy(Dali::Vector3(10.0f, 5.0f, 0.0f));

  // Rotate the actor relative to its current orientation
  actor.RotateBy(Dali::Degree(45.0f), Dali::Vector3::ZAXIS);

  // Retrieve size for layout logic
  Dali::Vector3 size = actor.GetTargetSize();
}
```

→ See: [Dali::[Layer](./layer.md)]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/object)
