---
id: addons
title: "addons"
sidebar_label: "addons"
---
## Introduction to DALi AddOns

The DALi AddOns framework provides a modular, dynamic mechanism for extending the engine's capabilities without modifying the core codebase. By utilizing shared library injection, AddOns allow developers to introduce platform-specific optimizations, hardware-specific features, or extended functionality that loads transparently during engine startup.

AddOns are distinct from standard application logic because they reside at the engine abstraction level. They are designed for scenarios where you need to hook into the low-level [update](./update.md)/render pipeline or provide specialized hardware interface implementations that are otherwise inaccessible from the standard application layer.

## AddOn Lifecycle and Loading Mechanism

The lifecycle of an AddOn is managed by the engine’s internal loader, which discovers and initializes modules during the early stages of DALi initialization. This sequence ensures that any core engine hooks are established before the main application loop commences.

The discovery process typically scans designated paths for shared objects (.so files) that implement the expected AddOn export symbols. Upon loading, the engine verifies the AddOn's build metadata and resolves the dispatch table to establish communication.

## AddOnBase Interface and Contract

The `AddOnBase` interface serves as the foundational contract for all custom AddOn implementations. Developers must inherit from this base to provide the mandatory entry points that the DALi engine requires to manage the [object](./object.md)'s lifecycle and task execution.

> Note: All AddOn implementations must be thread-safe regarding their own internal state, as they may be queried by different engine threads.

## The Dispatch Table Architecture

The `Dali::AddOns::DispatchTable` architecture is the core mechanism used for high-performance communication between the DALi engine and the AddOn. It utilizes `Dali::AddOns::DispatchTable::Entry` to map engine-requested operations to specific function pointers within the AddOn library.

By registering these entries, the AddOn exposes a stable API surface that the engine can invoke across the binary boundary without the overhead of complex messaging systems.

## AddOn Build Information and Metadata

Each AddOn must provide a `Dali::AddOnInfo::BuildInfo` structure, which the engine inspects to ensure binary compatibility. This metadata includes versioning information and build timestamps that prevent the engine from loading mismatched or legacy AddOn modules.

Validating this metadata is a critical step; failure to provide correct build information will result in the engine rejecting the module to preserve system stability.

## Thread Safety and Concurrency Constraints

AddOns operate within the multi-threaded environment of the DALi engine. Developers must strictly observe thread affinity: while initialization may occur on the main thread, specific engine hooks may be invoked from the [update](./update.md) or render threads.

> Warning: Never perform heavy blocking operations or direct UI manipulation from within an AddOn's internal callback if it is executing on the render thread, as this will lead to frame drops and visual stuttering.

## Integration with the Core Engine

AddOns interface with `Dali::Integration::Core` to hook into the main loop. Through this integration, an AddOn can register observers or specialized handlers that trigger during the [update](./update.md), commit, or render phases of the DALi frame cycle.

This allows the AddOn to maintain synchronized state with the scene graph, ensuring that custom hardware or software hooks remain aligned with the engine's [rendering](./rendering.md) state.

## Advanced Integration Patterns

Stateful AddOns often require careful management of graphics contexts and input event dispatching. When dealing with specialized graphics resources, the AddOn must ensure that context sharing is correctly handled and that cleanup routines are invoked via the destructor to prevent memory leaks.

Effective AddOn development involves minimizing the frequency of crossing the boundary between the engine and the AddOn. Batching operations through the `DispatchTable` is highly recommended to maintain high performance and ensure smooth engine execution.

### Example: Implementing a Custom AddOn Lifecycle

This example demonstrates the structure of an AddOn class and its interface with the engine.

```cpp
#include <dali/devel-api/addons/addon-base.h>

class MyCustomAddOn : public Dali::AddOnBase
{
public:
  MyCustomAddOn() = default;
  ~MyCustomAddOn() override = default;

  // Implementation of the contract for engine lifecycle hooks
  void Initialize() override
  {
    // Register dispatch table entries here
  }

  void OnUpdate() override
  {
    // Perform internal logic hooked into the engine's update cycle
  }
};

// Exported creation function required by the engine loader
extern "C" {
  Dali::AddOnBase* CreateAddOn()
  {
    return new MyCustomAddOn();
  }
}
```

→ See: [Dali::Accessibility::Accessible] (For examples of interface-based integration if the AddOn extends accessibility features).

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/addons)
