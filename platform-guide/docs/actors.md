---
id: actors
title: "Actor (Scene Graph Node)"
sidebar_label: "Actor (Scene Graph Node)"
---
## Introduction to DALi Actors

The DALi Actor hierarchy serves as the fundamental building block for the scene graph, representing a spatial node capable of participating in [rendering](./rendering.md), layout, and event propagation. While `Dali::Actor` provides the core scene graph functionality, application developers interact with the UI framework through `Dali::Ui::View`, which wraps these actor capabilities into a managed, high-level interface.

Using `Dali::Ui::View` allows you to leverage the engine's optimized layout and [rendering](./rendering.md) pipeline while maintaining a clean separation between raw scene graph nodes and your UI logic. You should use `View` for all standard application components, reserving the underlying `Actor` concepts only when manipulating the scene hierarchy or performing complex transformations that are not explicitly surfaced by higher-level UI controls.

## Scene Graph Lifecycle and Threading

The scene graph operates on a strictly defined lifecycle where actors are created, added to the stage, and eventually destroyed. Understanding the ownership model is critical to ensuring that your application remains responsive and free of memory leaks.

### Lifecycle Management
Actors in DALi are reference-counted handles. When you create a `View` (and by extension, the underlying `Actor`), the engine maintains an internal reference. An actor becomes active in the scene graph only when it is parented to a node currently connected to the root stage.

> Note: While `Dali::Actor` uses an internal reference count, `Dali::Ui::View` manages these lifecycles to ensure that when a view is removed from the UI hierarchy, its associated resources are cleaned up appropriately.

### Threading Considerations
The DALi scene graph is primarily accessed from the main event thread. While the engine handles multi-threaded [rendering](./rendering.md) internally, all modifications to the hierarchy—such as adding/removing children or updating transform properties—must be performed from the main thread to prevent race conditions during the frame synchronization phase.

## Transformations and Coordinate Systems

Actors utilize a parent-child coordinate system where every transformation applied to a parent node is propagated to its children. A `View` simplifies this by providing explicit API calls to manipulate these transformations relative to the coordinate space of the parent.

### Spatial Manipulation
You can move, rotate, or scale views using relative methods that [update](./update.md) the underlying actor's transform matrix.

**Example: Manipulating a View's Transform**
```cpp
// Assuming 'myView' is a member variable of type Dali::Ui::View
void UpdateViewTransform(Dali::Ui::View& myView)
{
  // Move the view by 50 pixels in the X direction
  myView.TranslateBy(Dali::Vector3(50.0f, 0.0f, 0.0f));

  // Apply a rotation of 45 degrees around the Z axis
  myView.RotateBy(Dali::Degree(45.0f), Dali::Vector3(0.0f, 0.0f, 1.0f));

  // Scale the view to 1.5 times its original size
  myView.ScaleBy(Dali::Vector3(1.5f, 1.5f, 1.0f));
}
```

### Coordinate Conversion
The `ScreenToLocal` method is vital for mapping input events (received in global screen coordinates) to a specific view's local coordinate space.

* **What:** Converts screen-space coordinates (x, y) into the local coordinate system of the actor.
* **Why:** Use this to accurately detect where a touch occurred relative to the view's origin, especially after applying complex transformations or nested hierarchies.
* **How:** Provide the target local float references. The method returns `true` if the conversion succeeds.

## Sub-Components Overview

The actor feature family includes specialized nodes designed for specific roles within the scene graph.

* **CameraActor:** Defines the view frustum and projection settings used to render the scene graph to the display. → See: `camera-actor`
* **CustomActor:** Enables the creation of user-defined rendering behaviors by overriding the default draw cycle. → See: `custom-actor`
* **DrawableActor:** Optimized for static or simple dynamic content that requires direct resource binding. → See: `drawable-actor`
* **Layer:** Provides a distinct depth-sorted container for grouping actors and applying global visual effects like opacity or blending. → See: `layer`

## Advanced Actor Properties and Policies

Performance and layout correctness depend on configuring how actors interact with the layout engine and the parent hierarchy.

### Resize Policy
The `SetResizePolicy` method controls how a view calculates its dimensions.
* **What:** Defines the relationship between the view's size and its parent's size.
* **Why:** Use `ResizePolicy` to create adaptive UIs that handle different screen aspect ratios without hardcoded pixel values.
* **How:** Set the policy type for a specific dimension (e.g., `ResizePolicy::FIXED`, `ResizePolicy::SIZE_RELATIVE_TO_PARENT`).

```cpp
void ConfigureViewLayout(Dali::Ui::View& myView)
{
  // Ensure the view width is always 50% of the parent width
  myView.SetResizePolicy(Dali::ResizePolicy::SIZE_RELATIVE_TO_PARENT, Dali::Dimension::WIDTH);
}
```

## Integration API for Engine Developers

For platform-level extensions, developers may need to interface with the `integration-api` or access properties not exposed by the standard `View` interface.

### Handling Signals
While `View` masks many internal signals, advanced integrations may require listening to `OnSceneSignal` or `OffSceneSignal` to synchronize native resource allocations with the view's lifecycle.

> Warning: Directly manipulating the scene graph via `Dali::Actor` methods on a `View` can bypass the view's internal state management. Only perform these actions when building custom UI components that extend the DALi framework itself.

### Finding Nodes
Use `FindChildByName` or `FindChildById` to perform deep searches in complex, dynamically generated UI trees when you need to re-bind or [update](./update.md) specific sub-components. These methods traverse the entire descendant tree, so they should be used sparingly during initialization rather than within a per-frame [update](./update.md) loop.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/actors)
