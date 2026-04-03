---
id: object
title: "object"
sidebar_label: "object"
---
## Introduction to the DALi Object System

The DALi [object](./object.md) system serves as the foundational architecture for all UI components within the framework. It utilizes a handle-based memory management model, allowing developers to interact with complex internal objects through lightweight, reference-counted proxies.

This system is designed to provide a consistent interface for managing the lifecycle, hierarchy, and properties of UI elements, such as `Dali::Actor`. By leveraging handles, DALi ensures that resource cleanup is managed automatically, preventing [common](./common.md) memory leaks associated with manual [object](./object.md) tracking in dynamic GUI environments.

## Working with Handles and Object Lifecycles

In DALi, a handle acts as a smart pointer to the underlying [object](./object.md). When you create an [object](./object.md), you receive a handle that you can pass around, copy, or assign, while the framework ensures the actual instance persists as long as at least one handle remains.

### Managing Actor Lifecycles
The `Dali::Actor` is the primary [object](./object.md) type in DALi, representing a node in the scene graph. You manage its lifecycle by creating instances and adding them to the scene hierarchy.

**API Usage:**
- `Actor()`: Creates an uninitialized handle. Use this to declare a member variable before assigning a real instance.
- `operator=`: Used to assign one handle to another, incrementing the internal reference count.

```cpp
#include <dali/dali.h>

void ManageActorExample() {
    // Creating an uninitialized handle
    Dali::Actor myActor;
    
    // Initializing with New() (typically provided by specific subclasses)
    myActor = Dali::Actor::New();
    
    // Copying a handle - both handles now point to the same underlying object
    Dali::Actor handleCopy = myActor;
    
    // When handles go out of scope, the underlying object is cleaned up 
    // if no other references exist.
}
```

## Dynamic Properties and Value Containers

Properties allow you to retrieve and modify the state of an object dynamically without needing to know the specific type of that object at compile time.

> Note: While property access is central to DALi objects, direct manipulation of raw property maps is a platform-level detail. Developers should typically interact with objects via their specialized public API methods.

## Property Notifications and Conditional Logic

Reactive UIs require the ability to respond to changes in object states. Property notifications enable you to define conditions that, when met, trigger specific callbacks within your application logic.

> Note: Configuration of `PropertyNotification` and `PropertyCondition` logic involves platform-level details regarding the signal/slot architecture. Please refer to the platform guide for binding callbacks to object property triggers.

## Type Registry and Runtime Type Information

The TypeRegistry is the central repository for metadata about all registered DALi objects. It allows you to query whether an object supports specific properties or actions at runtime.

> Note: Accessing the `TypeRegistry` directly for custom component registration is a platform-level detail. Use the standard object hierarchy for common interactions.

## Observing Object States

Monitoring the lifecycle and state transitions of objects is essential for robust resource management. The `BaseObjectObserver` provides a standardized way to track when objects are created, destroyed, or undergo significant state changes.

> Note: Implementation of `BaseObjectObserver` is a platform-level detail intended for advanced resource tracking. Standard application logic should rely on scene graph connection signals, such as `OnSceneSignalType` or `OffSceneSignalType`, available on `Dali::Actor`.

---

## Appendix: Actor Hierarchy Management

The `Dali::Actor` provides a robust set of methods for managing parent-child relationships and spatial positioning.

### Hierarchical Organization
You can organize your UI by nesting actors within one another.

**Example: Building a Hierarchy**
```cpp
#include <dali/dali.h>

void BuildHierarchy() {
    Dali::Actor root = Dali::Actor::New();
    Dali::Actor child = Dali::Actor::New();

    // Adding a child to the root
    root.Add(child);

    // Retrieving hierarchy info
    uint32_t count = root.GetChildCount(); // Returns 1
    Dali::Actor parent = child.GetParent(); // Returns root

    // Removing a child
    root.Remove(child);
}
```

### Spatial Transformations and Positioning
Actors support relative transformations and layout adjustments, ensuring UI elements scale and position correctly based on their parent's state.

**Example: Transforming Actors**
```cpp
#include <dali/dali.h>

void TransformActor(Dali::Actor actor) {
    // Apply relative translation
    actor.TranslateBy(Dali::Vector3(10.0f, 0.0f, 0.0f));

    // Apply rotation
    actor.RotateBy(Dali::Degree(45.0f), Dali::Vector3::ZAXIS);

    // Set resize policy for responsive layout
    actor.SetResizePolicy(Dali::ResizePolicy::FILL_TO_PARENT, Dali::Dimension::WIDTH);
}
```

> Warning: Always ensure that an actor is added to the scene graph (ultimately rooted to the Stage) if you expect it to be rendered. Using `Unparent()` effectively hides the actor and removes it from the render pipeline.

→ See: [Dali::[Layer](./layer.md)]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/object)
