---
id: atspi-interfaces
title: "atspi-interfaces"
sidebar_label: "atspi-interfaces"
---
## Introduction to ATSPI Interfaces

The `atspi-interfaces` module serves as the bridge between the Samsung DALi scene graph and the Assistive Technology Service Provider Interface (ATSPI) standard. It enables screen readers and other accessibility tools to interpret, navigate, and interact with DALi UI components by exposing them as a hierarchical tree of accessible objects.

You should use these interfaces whenever you need to ensure your custom UI controls are accessible to users with visual or motor impairments. What makes this module distinct is its ability to map high-level DALi `Actor` properties into standard accessibility roles and states, allowing the engine to automate much of the synchronization between the visual state and the accessibility bus.

## Internal Architecture and Type Mapping

The `atspi-interfaces` module relies on the `AtspiInterfaceTypeHelper` template system to handle the registration and reflection of accessibility types. This infrastructure allows the DALi engine to query an [object](./object.md) for its supported features at runtime without requiring hardcoded static casts to specific interface types.

### Interface Reflection and Discovery

The engine uses reflection to determine which interfaces an [object](./object.md) supports, ensuring that accessibility tools only interact with implemented features. Developers access these via the `Accessible` base class methods.

*   **GetInterfaces()**: Retrieves a collection of all currently implemented interfaces for the [object](./object.md).
*   **GetInterfacesAsStrings()**: Returns a list of interface names as strings, primarily for debugging or diagnostic tools that interface directly with the D-Bus representation of the [object](./object.md).

```cpp
// Example: Querying implemented interfaces
void InspectAccessible(Dali::Accessibility::Accessible* accessible) {
  auto interfaces = accessible->GetInterfacesAsStrings();
  for (const auto& name : interfaces) {
    printf("Implemented Interface: %s\n", name.c_str());
  }
}
```

## Accessibility Lifecycle Management

Accessibility objects in DALi follow a strict lifecycle managed by the engine’s accessibility controller. Objects must be properly initialized and linked to the DALi scene graph to ensure their addresses on the ATSPI bus remain valid.

### Initialization and Teardown

Lifecycle management is primarily governed by the `Accessible` base class. When an object is created, it should initialize its default accessibility features using the `InitDefaultFeatures()` method to populate the required ATSPI properties.

*   **InitDefaultFeatures()**: Call this to bootstrap the default accessibility state for a new component.
*   **Destructor**: The `~Accessible()` destructor ensures that when the DALi `Actor` is destroyed, the corresponding accessibility node is unhooked from the bus, preventing dangling pointers in the screen reader's cache.

> Warning: Failure to initialize default features may cause screen readers to ignore the component entirely, as it will lack the mandatory identity information required by the ATSPI bus.

## Thread Safety and Message Dispatching

Accessibility events are dispatched across process boundaries, necessitating a thread-safe approach to state updates. DALi handles the heavy lifting, but developers must ensure that state changes initiated from background threads are properly synchronized.

### Post-Render Synchronization

The `SetListenPostRender(bool enabled)` method is critical for performance. It tells the engine to wait for the next render pass before finalizing the state update to be sent to the ATSPI bus.

*   **SetListenPostRender**: Call this with `true` to ensure that accessibility events are consistent with the frame state. This prevents race conditions where the screen reader reports a state that has not yet been visually rendered.

```cpp
// Example: Configuring post-render notifications
void UpdateAccessibleState(Dali::Accessibility::Accessible* accessible) {
  // Sync the accessibility update with the rendering pipeline
  accessible->SetListenPostRender(true);
}
```

## Core ATSPI Role Interfaces

The `Accessible` class is the root for all accessibility-enabled objects. It provides the base methods required to build the accessibility tree and identify the object's purpose within the UI.

### Identity and Hierarchy Navigation

To allow a screen reader to traverse your UI, you must implement the hierarchy methods that define the relationship between objects.

*   **GetParent()**: Returns the parent `Accessible` object.
*   **GetChildCount()**: Returns the number of accessible children.
*   **GetChildren()**: Returns a vector of pointers to all direct children.
*   **GetChildAtIndex(std::size_t index)**: Returns the child at the specified index.
*   **GetIndexInParent()**: Returns the current object's position relative to its parent’s children.

```cpp
// Example: Navigating the tree
void LogChildren(Dali::Accessibility::Accessible* parent) {
  std::size_t count = parent->GetChildCount();
  for (std::size_t i = 0; i < count; ++i) {
    auto child = parent->GetChildAtIndex(i);
    // Process child...
  }
}
```

## Interaction and Data Manipulation Interfaces

Interactive components (buttons, sliders, toggles) must provide ways for assistive technology to manipulate them beyond simple focus.

### Gesture and Pointing Support

Accessibility tools often simulate user input through specific interfaces that target the spatial representation of an object.

*   **DoGesture()**: Allows an external agent to trigger an action (e.g., a "click" or "swipe") on the accessible object by passing a `GestureInfo` struct.
*   **GetAccessibleAtPoint()**: Returns the child object at a specific screen coordinate, which is essential for screen readers using "touch exploration" mode.

```cpp
// Example: Using GetAccessibleAtPoint
void GetTarget(Dali::Accessibility::Accessible* root, Dali::Accessibility::Point pt) {
  auto target = root->GetAccessibleAtPoint(pt, Dali::Accessibility::CoordinateType::WINDOW);
  if (target) {
    // Process the accessible object found under the touch
  }
}
```

## Text and Hyperlink Content Interfaces

For components containing textual data, the `[atspi-interfaces](./atspi-interfaces.md)` module provides specific hooks for range-based reading and navigation.

### Property Access

Accessing raw text properties and document structure is managed through the `GetStringProperty` interface, which provides a generic way to retrieve specific text-related metadata (like current selection ranges or text formatting attributes).

*   **GetStringProperty(std::string propertyName)**: Used to retrieve specific text properties such as "text", "text-selection", or "font-name".

> Note: Always verify if an object supports a text-based interface before requesting text properties; querying non-textual components may result in empty strings.

## Integration API Usage Patterns

For deep engine integration, the `Integration API` exposes the ability to inject custom features into the `Accessible` object. This is typically used by engine developers to create custom roles or specialized feature sets.

### Feature Extensibility

The `AddFeature` and `GetFeature` methods allow you to attach and retrieve custom functionality containers (e.g., specific accessibility protocols or advanced state management modules).

*   **AddFeature(std::shared_ptr<Accessible> accessible)**: Integrates a new feature into the existing node.
*   **GetFeature<T>()**: Retrieves an attached feature of type `T`.

```cpp
// Example: Accessing a specialized feature
// Assuming CustomFeature is a derived class of Accessible
auto feature = myAccessible->GetFeature<CustomFeature>();
if (feature) {
  // Execute custom accessibility logic
}
```

→ See: [Dali::Actor]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/atspi-interfaces)
