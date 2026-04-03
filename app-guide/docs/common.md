---
id: common
title: "common"
sidebar_label: "common"
---
## Introduction to Common Utilities

The DALi `common` module provides a foundational layer of [utility](./utility.md) classes, containers, and helpers designed to enforce type safety and optimize performance across your UI application. By utilizing these standardized tools, developers can ensure consistent memory management and data handling while minimizing the boilerplate code typically required for complex graphical interfaces.

These utilities are essential when building robust DALi components, as they integrate directly with the framework’s [object](./object.md) lifecycle and [threading](./threading.md) models. You should use the `common` module to handle strings, manage [object](./object.md) references, and maintain consistent coordinate data structures throughout your application architecture.

## Memory Management and Smart Pointers

Efficient memory management in DALi is achieved through managed pointers that track [object](./object.md) lifetimes and prevent leaks in complex scene graphs. These utilities automate the cleanup of DALi objects, ensuring that resources are released as soon as they are no longer reachable by the application or the framework.

> Note: DALi objects (like `Actor`) are handle-based. The memory management provided by the `common` module is specific to the DALi handle/proxy pattern.

## Container and Data Structures

The `common` module provides optimized data structures designed to minimize memory fragmentation and overhead when storing large collections of UI elements or properties. These containers are the preferred way to manage [object](./object.md) hierarchies and data buffers within DALi.

→ See: [Dali::Actor]

## String Handling and Utilities

Text processing in DALi is handled through specialized string classes that prioritize performance and safe memory access. `Dali::StringView` provides a lightweight, non-owning reference to character data, which is ideal for performance-critical path processing.

### StringView Usage

`Dali::StringView` allows you to pass string data to methods without incurring the cost of deep string copies. This is particularly useful when searching for objects in a large scene graph.

```cpp
#include <dali/public-api/actors/actor.h>

void FindAndProcess(Dali::Actor root, const char* name)
{
  // Using StringView to search for an actor by name efficiently
  Dali::StringView searchName(name);
  Dali::Actor foundActor = root.FindChildByName(searchName);
  
  if(foundActor)
  {
    // Actor found, proceed with logic
  }
}
```

## Type Traits and Compile-Time Helpers

DALi utilizes template-based type traits to facilitate generic programming, allowing your code to adapt to different UI data types at compile time. These utilities ensure that your custom logic remains type-safe when interacting with properties or animation targets.

## Global Services and Singletons

The `SingletonService` provides a centralized mechanism for accessing global state and framework-level resources. It ensures that critical services remain globally available while maintaining a controlled lifecycle for the application environment.

## Geometric and Utility Constants

DALi uses standardized geometric structures to define spatial properties and coordinate configurations, ensuring consistency when placing or sizing UI elements. These structures are the building blocks for all layout operations.

### Understanding Actor Size and Geometry

Spatial awareness is handled by fetching natural and target sizes from `Dali::Actor`. These methods allow you to query the state of an element within the scene hierarchy.

```cpp
#include <dali/public-api/actors/actor.h>
#include <dali/public-api/math/vector3.h>

void LogActorDimensions(Dali::Actor actor)
{
  // Retrieve the natural size defined by the actor's content
  Dali::Vector3 naturalSize = actor.GetNaturalSize();
  
  // Retrieve the current target size after layout processing
  Dali::Vector3 targetSize = actor.GetTargetSize();
  
  // Use these values to calculate dynamic layouts
}
```

## Error Handling and Bitmask Utilities

The `[common](./common.md)` module includes tools to handle framework errors and manage state flags using bitmask operators. These utilities allow for clean, readable code when dealing with complex configuration flags or status reporting.

### Bitmask Utility Usage

The `EnableBitMaskOperators` utility allows you to perform logical operations on enum-based flags, providing a safe way to manage state configurations in your application.

> Warning: Always ensure that your flag enums are designed for bitwise operations when using these utilities to avoid undefined behavior during logical comparisons.

### Error Reporting with DaliException

When unexpected states occur during the application lifecycle, `DaliException` serves as the primary mechanism for signaling errors.

```cpp
#include <dali/public-api/common/dali-exception.h>

// Throwing a standard DALi exception when a critical precondition is not met
void ValidateActor(Dali::Actor actor)
{
  if(!actor)
  {
    throw Dali::DaliException("Actor handle is empty", "InvalidActorState");
  }
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/common)
