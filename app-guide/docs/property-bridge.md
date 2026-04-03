---
id: property-bridge
title: "property-bridge"
sidebar_label: "property-bridge"
---
## Introduction to Property Bridge

The Property Bridge is a specialized DALi module designed to facilitate dynamic data synchronization between [object](./object.md) properties. It acts as a reactive middleware layer, allowing developers to map property changes from a source [object](./object.md) to a destination [object](./object.md), ensuring UI components and data models remain in perfect alignment without manual polling or redundant signal connections.

You should use the Property Bridge when your application requires automated data flow between complex UI components or when keeping multiple objects synchronized would otherwise result in significant boilerplate code. It is distinct from standard signal-slot mechanisms because it encapsulates the state-propagation logic, offering a declarative approach to [object](./object.md) interaction.

## Setting Up Property Connections

Establishing a connection requires defining the roles of the participating objects and initializing the bridge structure to handle the lifecycle of the link. This section outlines the essential configuration steps to bring your [property-bridge](./property-bridge.md) instance into an active state.

### Initializing the Bridge
The initialization process involves targeting the source [object](./object.md) and the destination [object](./object.md) that will participate in the data synchronization.

> Note: Ensure that both source and destination objects are valid handle instances before attempting to attach them to the bridge; otherwise, the bridge will fail to establish the necessary internal references.

```cpp
#include <dali/dali.h>

// Example: Initializing the bridge between two visual elements
Dali::Actor sourceActor = Dali::Actor::New();
Dali::Actor targetActor = Dali::Actor::New();

// The bridge acts as the mediator for these two handles
// Implementation follows the specific bridge pattern for your DALi version
```

## Binding and Mapping Properties

Binding properties is the core operation of the Property Bridge, where you define which specific data points should propagate updates. By mapping keys, you create a direct pipeline where changes to the source attribute are automatically reflected in the destination.

### Mapping Property Keys
Mapping ensures that the Bridge knows exactly which property index on the source object corresponds to the target index on the destination object.

> Warning: Performance may be impacted if you bind properties that trigger frequent layout updates, such as high-frequency animation properties or complex layout attributes.

```cpp
// Mapping a property index from source to target
// Ensure index compatibility between the source and target types
const Dali::Property::Index sourceIndex = Actor::Property::COLOR;
const Dali::Property::Index targetIndex = Actor::Property::COLOR;

// Configuration of the mapping logic
// propertyBridge.AddBinding(sourceActor, sourceIndex, targetActor, targetIndex);
```

## Managing Bridge Lifecycle

Proper management of the Property Bridge lifecycle is critical for memory safety and preventing unnecessary background processing. Creating and destroying bridges should strictly follow the scope of the objects they are bridging.

### Handling Bridge Cleanup
When the associated objects are removed from the scene or destroyed, the bridge must be explicitly cleared to prevent dangling pointers or memory leaks.

```cpp
// Best practice: encapsulate the bridge in a manager or a scope
void CleanupBridge(Dali::PropertyBridge& bridge)
{
  // Explicitly remove bindings to disconnect references
  bridge.RemoveAllBindings();
}
```

## Handling Property Updates

The Property Bridge provides mechanisms to observe state changes, allowing your application logic to react when data synchronization occurs. This is vital for implementing side effects, such as updating UI overlays when a data model changes.

### Observing Synchronization
By hooking into the bridge's update flow, you can perform validation or secondary logic immediately after a property value has been propagated.

```cpp
// Example: Responding to a bridge synchronization event
void OnBridgeUpdated(Dali::PropertyBridge& bridge)
{
  // Custom logic triggered after successful propagation
}
```

## Advanced Data Synchronization Patterns

For complex UI architectures, you can chain property bridges or use intermediate objects to manage multifaceted data dependencies. This pattern ensures that cross-object consistency is maintained even when multiple objects share state.

### Multi-Object Chaining
You can link multiple objects by cascading bridges, where the destination of one bridge acts as the source for another.

> Note: Chaining should be used judiciously; long chains of synchronized properties can introduce latency and debugging difficulty within your UI logic.

```cpp
// Pattern: A -> B -> C Synchronization
// Bridge 1: A to B
// Bridge 2: B to C
// Changes in A now propagate through the entire chain to C
```

→ See: [Property Notification System](link-to-notify-module) for alternative event-based synchronization methods.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/property-bridge)
