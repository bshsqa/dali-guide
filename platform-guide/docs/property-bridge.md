---
id: property-bridge
title: "property-bridge"
sidebar_label: "property-bridge"
---
## Introduction to PropertyBridge

The `PropertyBridge` is a specialized integration-layer component designed to facilitate high-performance data synchronization between the DALi engine core and external data providers. It serves as a bridge that allows developers to bridge the gap between engine-internal actor properties and external application-defined states.

You should use the `PropertyBridge` when you need to perform deep inspections or synchronized queries of actor property values that are otherwise abstracted or inaccessible via standard public-facing API patterns. It is distinct because it operates at the integration layer, providing direct hooks into the engine's internal property representation system.

## Internal Architecture and Data Flow

The `PropertyBridge` architecture relies on a messaging-based relay system defined within `property-bridge.h`. It acts as an interface layer that abstracts the complexity of the engine's property storage, allowing for efficient retrieval without triggering full event-loop stalls.

### Understanding the Message Relay
The bridge facilitates the translation of property lookup requests into engine-internal commands. By leveraging the `Internal::PropertyBridge` implementation, the `Ui::PropertyBridge` ensures that requests originating from the application layer are correctly routed to the property owner within the scene graph.

> Note: The `PropertyBridge` is a handle-based class. Interaction with the underlying implementation is managed via the `DALI_INTERNAL` constructor which links the public handle to the engine-side data structure.

## Lifecycle Management and Ownership

Managing the `PropertyBridge` involves initialization through the engine's provider methods and standard RAII-based cleanup. Ownership is handled by the DALi [object](./object.md) handle system, ensuring the bridge exists as long as the internal implementation requires it.

### Construction and Destruction
The bridge is instantiated via the `Get()` method, which acts as a singleton access point for the bridge instance.

- **`PropertyBridge()`**: The default constructor initializes an empty handle.
- **`~PropertyBridge()`**: The destructor ensures that all references to the underlying engine implementation are correctly released.
- **`PropertyBridge(Internal::PropertyBridge *impl)`**: A `DALI_INTERNAL` constructor used when the engine creates the bridge instance; developers should avoid calling this directly.

```cpp
#include <dali/devel-api/ui/property-bridge.h>

void SetupBridge() {
    // Acquire the instance of the property bridge
    Dali::Ui::PropertyBridge bridge = Dali::Ui::PropertyBridge::Get();
    // The bridge is now ready for property inspection
}
```

## Thread Safety and Concurrency Models

The `PropertyBridge` is designed to be thread-aware, reflecting DALi's split-thread architecture. While property retrieval via `GetStringProperty` is designed to be efficient, developers must ensure that the bridge is accessed on the appropriate thread (typically the Main/Update thread) to maintain data consistency.

### Cross-Thread Communication
The bridge internally handles the synchronization required to communicate with the Update thread. Requests made from the Application thread are marshaled through the engine's thread-safe command queue.

> Warning: Calling `PropertyBridge` methods from threads other than the DALi Main thread may result in race conditions or undefined behavior if the underlying actor is being modified simultaneously.

## Integration API Usage

The `PropertyBridge` provides specialized access to properties, specifically focusing on string-based property retrieval. This is vital for debugging visual states or synchronizing dynamic labels with engine-side state.

### Retrieving Actor Properties
The `GetStringProperty` method is the primary tool for querying an actor's current state.

- **`GetStringProperty(Actor actor, const std::string &propertyName)`**:
    - `actor`: The `Dali::Actor` handle to query.
    - `propertyName`: The string identifier of the property to retrieve.
    - **Return**: A `std::string` containing the property value. If the property does not exist or is not a string type, the return may be an empty string.

```cpp
#include <dali/public-api/actor/actor.h>
#include <dali/devel-api/ui/property-bridge.h>

void QueryActor(Dali::Actor myActor) {
    auto bridge = Dali::Ui::PropertyBridge::Get();
    
    // Retrieve a custom string property (e.g., "id" or "state")
    std::string value = bridge.GetStringProperty(myActor, "my-custom-property");
    
    if (!value.empty()) {
        // Process the retrieved property
    }
}
```

## Performance Constraints and Best Practices

Frequent polling via the `PropertyBridge` can introduce overhead in the Update thread. Because the bridge accesses internal property tables, excessive calls during the rendering frame should be avoided to prevent frame-rate drops.

> Note: Prefer caching the property values retrieved from the bridge if the underlying data does not change every frame. Do not use the bridge as a replacement for standard Observer or Signal/Slot mechanisms for real-time state changes.

## Troubleshooting and Debugging the Bridge

Common issues with `PropertyBridge` typically stem from incorrect property naming or querying actors that have been destroyed.

### Diagnostic Checklist
- **Invalid Handles**: Always check if the `Actor` handle is valid (`actor.GetBaseObject() != nullptr`) before calling `GetStringProperty`.
- **Property Existence**: If `GetStringProperty` returns an empty string, verify the property name against the actual property registration index of the target actor.
- **Implementation Mismatch**: Ensure that the `[property-bridge](./property-bridge.md).cpp` linked in your build environment matches the version of the DALi engine core being utilized, as mismatching integration APIs can lead to segmentation faults during property lookups.

→ See: [Dali::Actor]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/property-bridge)
