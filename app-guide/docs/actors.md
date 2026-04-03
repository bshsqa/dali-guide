---
id: actors
title: "Actor (Scene Graph Node)"
sidebar_label: "Actor (Scene Graph Node)"
---
## Introduction to Actors

Actors represent the foundational visual entities within the DALi framework. While developers primarily interact with `Dali::Ui::View` to manage UI elements, every `View` is internally powered by the `Actor` architecture, which handles the scene graph tree, spatial transformations, and [rendering](./rendering.md) lifecycle.

The actor system is designed to create a hierarchical scene graph where visual properties (like position or visibility) flow from parent to child. You should use the `View` API to construct your user interface, as it encapsulates the actor's raw functional complexity into a declarative, lifecycle-managed component suitable for modern application development.

## Transformations and Spatial Positioning

Spatial positioning defines where and how a UI element appears in the 3D space. While you typically use layout containers for alignment, you can perform manual transformations on `Dali::Ui::View` instances using underlying actor methods to create animations or custom visual effects.

### Relative Transformations
You can modify the current transformation of a view relative to its existing state. This is useful for incremental animations or small adjustments to an element's placement without calculating its absolute world coordinates.

*   **`TranslateBy(const Vector3 &distance)`**: Moves the view by a specific vector.
*   **`RotateBy(...)`**: Applies a rotation using degrees, radians, or a quaternion.
*   **`ScaleBy(const Vector3 &relativeScale)`**: Adjusts the view size relative to its current scale.

```cpp
// Example: Translating and scaling a View
auto myView = Dali::Ui::View::New();
// Move the view 50 pixels to the right
myView.TranslateBy(Dali::Vector3(50.0f, 0.0f, 0.0f));
// Scale the view to 1.5x its original size
myView.ScaleBy(Dali::Vector3(1.5f, 1.5f, 1.0f));
```

### Coordinate Conversion
Sometimes you need to map global input coordinates (e.g., from a touch event) to the local coordinate system of a specific view.

*   **`ScreenToLocal(float &localX, float &localY, float screenX, float screenY)`**: Converts global screen coordinates to the view's local space. The function returns `true` if the conversion was successful, allowing you to handle input relative to a specific UI component.

## Hierarchy and Scene Management

The scene graph is a tree of actors. By using `Dali::Ui::View`, you manage this hierarchy to organize how UI elements are layered and rendered.

### Parent-Child Relationships
Every `View` can contain other views as children. When a parent view is moved, hidden, or deleted, its children are affected accordingly.

*   **`Add(Actor child)`**: Adds a new view as a child to the current view.
*   **`Remove(Actor child)`**: Removes a child view from the current view.
*   **`Unparent()`**: Detaches the view from its current parent.

> **Note:** Always use `Dali::Ui::View` objects when calling these methods. Using raw `Dali::Actor` pointers directly is generally discouraged for UI composition.

### Managing Z-Order and Depth
The order in which siblings are added determines their rendering order. You can adjust this depth dynamically.

*   **`Raise()` / `Lower()`**: Moves the view up or down by one position in the stack.
*   **`RaiseToTop()` / `LowerToBottom()`**: Moves the view to the extreme front or back of its siblings.
*   **`RaiseAbove(Actor target)` / `LowerBelow(Actor target)`**: Explicitly places the view relative to another specific sibling.

```cpp
auto parentView = Dali::Ui::View::New();
auto child1 = Dali::Ui::View::New();
auto child2 = Dali::Ui::View::New();

parentView.Add(child1);
parentView.Add(child2);

// Bring child1 to the front so it is drawn over child2
child1.RaiseToTop();
```

## Event Handling and Signals

Actors provide the signaling infrastructure for interactions. While `Dali::Ui::View` abstracts many high-level events, you can connect to the underlying actor signals for low-level interaction requirements.

### Standard Signals
These signals allow you to react to user input and lifecycle changes:
*   **`Touched`**: Triggered by pointer/touch interactions.
*   **`Hovered`**: Triggered when a pointer enters or leaves the actor area.
*   **`WheelEvent`**: Triggered by scroll wheel input.
*   **`OnScene` / `OffScene`**: Fired when the actor is added to or removed from the active stage.

```cpp
// Example: Connecting to a touch event
myView.TouchedSignal().Connect([](Dali::Actor actor, const Dali::TouchEvent& event) -> bool {
    // Handle touch logic here
    return true; // Return true to consume the event
});
```

## Sub-Components Overview

Specialized actor types provide specific functionality for rendering and scene structure.

*   **CameraActor**: Defines the viewpoint and projection properties used to view the scene. → See: `camera-actor`
*   **CustomActor**: A base for creating complex, specialized UI elements that require custom rendering logic. → See: `custom-actor`
*   **DrawableActor**: An actor optimized for displaying simple visual geometry or assets. → See: `drawable-actor`
*   **Layer**: A high-level container used to group actors into distinct rendering planes, controlling the Z-order of entire screen sections. → See: `layer`

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/actors)
