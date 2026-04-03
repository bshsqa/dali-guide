---
id: common
title: "common"
sidebar_label: "common"
---
## Introduction to the DALi Common Module

The `common` module serves as the foundational bedrock of the DALi engine, providing essential cross-platform abstractions, memory management primitives, and [utility](./utility.md) headers that ensure consistent behavior across varied hardware. It acts as the shared infrastructure layer, decoupling the higher-level GUI components from platform-specific implementation details.

Developers should utilize the `common` module when building engine extensions, custom data structures, or performance-critical logic that requires low-level control over memory, type safety, or cross-platform portability. It is distinct from other DALi modules by focusing on systemic stability and efficiency rather than visual presentation or input processing.

## Memory Management and Pointer Semantics

The DALi memory management subsystem ensures deterministic [object](./object.md) lifecycles and provides robust alternatives to standard library smart pointers, tailored specifically for the engine's performance needs. These tools are critical for preventing leaks in the complex [object](./object.md) graphs typically found in GUI applications.

### IntrusivePtr and Reference Counting
DALi heavily relies on intrusive reference counting to track [object](./object.md) ownership in large hierarchies. By embedding the reference counter within the [object](./object.md) itself, DALi achieves superior cache locality and avoids the double-allocation overhead associated with standard `std::shared_ptr`.

> Note: Always prefer DALi-provided smart pointers when dealing with `Dali::Actor` or derived engine objects to ensure compatibility with the engine's internal [object](./object.md) tracking systems.

## Template Metaprogramming Utilities

The `common` module exposes a suite of template metaprogramming (TMP) tools that enable the creation of highly generic yet type-safe interfaces. These utilities allow the compiler to generate specialized, optimized code paths at compile-time, reducing runtime overhead in frequently executed code paths like layout calculation or event dispatch.

### Type Trait Helpers
Using traits like `EnableIf` and `IsConvertible`, developers can restrict template function participation to specific [object](./object.md) types. This ensures that errors are caught at compile-time rather than during execution, which is vital for maintaining engine stability across different compiler versions.

## Data Containers and Optimized Structures

To meet the high-performance requirements of 60fps [rendering](./rendering.md), the `common` module provides custom container implementations designed to minimize memory fragmentation and allocation latency. These structures are tuned for scenarios where frequent additions and removals occur within the scene graph.

### Vector and RefCountedVector
`Vector` is the primary contiguous container in DALi, optimized for performance over the standard library's `std::vector` by employing more efficient re-allocation strategies. 

## Exception Handling and Error Diagnostics

The DALi error handling framework provides a centralized mechanism for reporting and recovering from fatal engine states. By leveraging `DaliException`, the engine ensures that critical failures can be caught and gracefully handled or logged before a platform-level crash occurs.

## Singleton and Add-On Lifecycle Services

Global engine services are managed through the `SingletonService`, which provides a thread-safe registry for components that must exist as a single instance throughout the application lifecycle. This mechanism prevents initialization races and ensures that core systems, such as the event processor or the [rendering](./rendering.md) backend, are initialized in the correct order.

## Platform-Specific Integration and Basic Types

The `common` module abstracts platform-level discrepancies into a unified API for fundamental types like strings and extents. These types provide the necessary bridge between raw system inputs and high-level engine representations, ensuring that data is normalized correctly regardless of the underlying operating system.

### Working with Dali::Actor (Basic Utility)
The `Dali::Actor` class is the fundamental unit of the DALi scene graph. While defined in core modules, its usage of [common](./common.md) types like `Dali::StringView` for naming and `Vector3` for geometric transformations exemplifies the integration of `common` primitives.

```cpp
#include <dali/public-api/actor/actor.h>
#include <dali/public-api/math/vector3.h>

void CreateAndConfigureActor() 
{
    // Create an uninitialized Actor using the default constructor
    Dali::Actor myActor = Dali::Actor::New();

    // Set properties using common math types
    myActor.SetResizePolicy(Dali::ResizePolicy::FIXED, Dali::Dimension::ALL_DIMENSIONS);
    
    // Add to scene graph
    Dali::Actor parentActor = GetRootActor(); // Assuming a root exists
    parentActor.Add(myActor);

    // Using FindChildByName with common StringView
    Dali::Actor found = parentActor.FindChildByName("target_node");
    
    if (found) {
        // Manipulate transformation
        found.TranslateBy(Dali::Vector3(10.0f, 0.0f, 0.0f));
    }
}
```

> Warning: `FindChildByName` performs a recursive search through the hierarchy. For performance-critical code in a deep tree, cache the `Actor` pointer instead of calling this repeatedly.

→ See: [Dali::Actor] for detailed interaction methods.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/common)
