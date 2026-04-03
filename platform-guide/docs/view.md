---
id: view
title: "View (Base UI Object)"
sidebar_label: "View (Base UI Object)"
---
## Introduction to the View Framework

The `Dali::Ui::View` system serves as the foundational building block for all visual elements within the DALi application environment. Unlike raw `Dali::Actor` objects, which provide low-level scene graph manipulation, `Dali::Ui::View` abstracts the complexities of layout, event handling, and resource management into a developer-friendly, unified interface. You should use `View` when constructing any UI element that requires interaction, declarative layout, or standardized styling, as it provides a stable bridge between the engine's core [rendering](./rendering.md) capabilities and high-level application logic.

## Internal Architecture and Lifecycle

`Dali::Ui::View` acts as a specialized wrapper around the `Dali::Actor`, inheriting transform and parent/child relationship capabilities while enforcing a structured lifecycle. When a `View` is instantiated, it registers itself within the DALi scene graph, ensuring that it participates in the engine's [update](./update.md), layout, and [rendering](./rendering.md) traversals.

The lifecycle consists of:
1. **Instantiation**: The `View` [object](./object.md) is created and associated with its internal backend handle.
2. **Mounting**: When added to a parent `View` or the root layer, it enters the active scene graph.
3. **[Layout](./layout.md)/Update**: The engine calculates the `View`'s geometry and updates state based on the current tick.
4. **Rendering**: The `View`'s visual data is submitted to the GPU.
5. **Destruction**: The `View` is disconnected, and its resources are released following the smart-pointer reference counting convention.

> Note: Always manage `View` lifecycles using handle-based reference counting; manual deletion is strictly prohibited to prevent dangling pointers in the engine's [update](./update.md) thread.

## Thread Safety and Concurrency

The `Dali::Ui::View` framework operates on a thread-affinity model where UI modifications must occur on the main (UI) thread. While the DALi engine performs [rendering](./rendering.md) on a separate background thread, all structural changes to the `View` tree—such as adding/removing children or updating transform properties—must be orchestrated by the application’s main thread.

To facilitate thread-safe communication, use the `Dali::CustomEvent` system or post tasks to the application’s message queue. This ensures that the engine’s internal state remains consistent during the transition between frames.

```cpp
// Thread-safe update pattern
void UpdateViewStyle(Dali::Ui::View view, Dali::Vector4 newColor) {
  // Always perform modifications on the Main Thread
  Dali::PostCallback([view, newColor]() {
    view.SetBackgroundColor(newColor);
  });
}
```

## Integration API and Native Bridging

For advanced use cases, such as custom native bindings or low-level engine extensions, `Dali::Ui::View` provides access to integration-level handles. These APIs allow developers to interface with platform-specific windowing systems or custom shader-based rendering paths.

When working at this tier, you bypass some of the high-level abstractions to gain direct control over the underlying `Actor` handle. Use `Dali::Ui::View::GetActor()` to retrieve the raw node for integration with native C++ or external graphics libraries, ensuring that you maintain the integrity of the scene graph hierarchy.

## Sub-Components Overview

The `View` framework is extended by several specialized components designed to handle specific UI requirements:

* **AnimatedImageView**: Optimized for sequences of bitmap frames with state control. → See: [AnimatedImageView]
* **ImageView**: The standard component for displaying textures, sprites, and static visual assets. → See: [ImageView]
* **InputField**: A specialized container providing text entry, focus management, and input validation. → See: [InputField]
* **Label**: A lightweight, high-performance text rendering engine optimized for static or dynamic strings. → See: [Label]
* **Layout**: A container component used to define coordinate systems and arrangement rules for children. → See: [Layout]
* **LottieAnimationView**: A high-fidelity rendering component for vector-based JSON animations. → See: [LottieAnimationView]
* **ScrollView**: A specialized container providing kinetic scrolling, clipping, and overflow management. → See: [ScrollView]

## Best Practices and Performance

To maintain optimal frame rates and memory efficiency, follow these architectural guidelines when building complex `View` hierarchies:

1. **Flatten Hierarchies**: Minimize the depth of your view tree. Excessive nesting increases the cost of transform propagation and matrix calculations.
2. **Clipping**: Use clipping boundaries effectively to prevent the engine from processing `Views` that are outside the viewport.
3. **Resource Caching**: Reuse image assets and styles across different `View` instances to reduce GPU memory pressure.
4. **Avoid Frequent Instantiation**: If a UI component is toggled frequently, hide/show the `View` (using visibility flags) rather than destroying and recreating it.

```cpp
#include <dali/dali.h>
#include <dali/ui/view.h>

// Example: Standard View usage pattern
void CreateUI(Dali::Ui::View parent) {
  Dali::Ui::View myView;
  
  // Configure visual properties
  myView.SetSize(200.0f, 200.0f);
  myView.SetPosition(50.0f, 50.0f);
  
  // Attach to the tree
  parent.Add(myView);
}
```

> Warning: Performance degradations often stem from "Overdraw"—[rendering](./rendering.md) overlapping transparent Views. Use solid background colors where possible to assist the engine in depth testing and early-Z rejection.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/view)
