---
id: absolute-layout
title: "AbsoluteLayout"
sidebar_label: "AbsoluteLayout"
---
## Introduction to [AbsoluteLayout](./absolute-layout.md)

The `AbsoluteLayout` controller provides a mechanism for positioning child elements at explicit, coordinate-based locations within a container. Unlike dynamic flow [layouts](./layouts.md), `AbsoluteLayout` offers full manual control over the `x`, `y`, `width`, and `height` properties of children, making it ideal for static UIs or designs where precise pixel-perfect placement is required regardless of container reflow.

You should use `AbsoluteLayout` when the relative position of UI components is fixed and does not require complex alignment logic, such as in game overlays or specialized HUD components. It is distinct from other layout variants by its reliance on the `AbsoluteLayoutParams` [object](./object.md), which maps directly to raw spatial coordinates rather than alignment constraints. → See: [Layouts]

## Initialization and Object Lifecycle

The `AbsoluteLayout` is managed via a handle-based lifecycle [common](./common.md) to the DALi framework, where the `New()` factory method is the preferred way to instantiate the [object](./object.md). Once initialized, the layout handle should be assigned to a layout container to begin influencing the positioning of its children.

### Managing the Lifecycle
The `New()` method initializes the internal layout engine and prepares it for property updates. Because `AbsoluteLayout` inherits from the base layout class, it remains valid as long as the container holding it is active within the scene graph.

```cpp
#include <dali/ui/absolute-layout.h>

void CreateLayout() {
  // Initialize a new AbsoluteLayout instance
  Dali::Ui::AbsoluteLayout myLayout = Dali::Ui::AbsoluteLayout::New();
  
  // The layout can be copied or moved; it maintains an internal reference-counted handle
  Dali::Ui::AbsoluteLayout anotherHandle = myLayout; 
}
```

> Note: Always use the `New()` static factory method rather than constructing the object directly, as the factory handles the underlying engine registration required for the layout to function.

## Type Casting and Integration

Integration with the DALi actor hierarchy requires safe downcasting of handles. Since `[AbsoluteLayout](./absolute-layout.md)` and its corresponding `AbsoluteLayoutParams` are part of a broader layout system, you will frequently transition between generic base handles and specific layout types.

### Using DownCast
The `DownCast` method allows you to safely cast a `BaseHandle` to an `[AbsoluteLayout](./absolute-layout.md)` or `AbsoluteLayoutParams` type. This is essential when retrieving layout parameters from child actors that have been registered within a layout container.

```cpp
#include <dali/ui/absolute-layout.h>

void SetupChild(Dali::Actor child) {
  // Create parameters specific to AbsoluteLayout
  Dali::Ui::AbsoluteLayoutParams params = Dali::Ui::AbsoluteLayoutParams::New();
  params.SetX(100.0f);
  params.SetY(200.0f);
  params.SetWidth(50.0f);
  params.SetHeight(50.0f);
  
  // In a real application, you would attach 'params' to the child actor's layout data
}

void ProcessHandle(Dali::BaseHandle handle) {
  // Safely cast back to AbsoluteLayoutParams
  Dali::Ui::AbsoluteLayoutParams params = Dali::Ui::AbsoluteLayoutParams::DownCast(handle);
  if (params) {
    float xPos = params.GetX();
  }
}
```

## Thread Safety and Execution Context

The `[AbsoluteLayout](./absolute-layout.md)` property updates are processed within the DALi update thread. While you can manipulate `AbsoluteLayoutParams` from the main thread during UI construction, modifications to layout properties during the animation loop must be synchronized with the engine's update cycle to prevent visual jitter.

### Property Threading
When setting bounds using `SetX`, `SetY`, `SetWidth`, or `SetHeight`, the changes are queued for the next layout pass. Do not perform high-frequency, frame-by-frame updates of these properties from secondary threads; instead, utilize DALi's animation system if movement is required.

> Warning: Calling `SetBounds` or individual setter methods frequently on every frame can trigger expensive layout invalidations. Use these methods primarily for initial configuration or state changes, not for continuous animation.

## Performance and Layout Constraints

The efficiency of `[AbsoluteLayout](./absolute-layout.md)` is derived from its O(1) coordinate calculation model. Because the engine does not need to compute relative dependencies, it is the most performant layout type for large, flat hierarchies.

### Optimizing Layout Passes
To minimize layout invalidations:
1. Batch multiple parameter updates (e.g., using `SetBounds` instead of four separate calls) whenever possible.
2. Ensure child actors are not triggering unnecessary re-layouts through other mechanisms like constraints or parent-child transformation changes.

```cpp
#include <dali/ui/absolute-layout.h>

void ConfigureChildBounds(Dali::Ui::AbsoluteLayoutParams& params) {
  // Use SetBounds to update all spatial properties in a single step
  Dali::LayoutRect newBounds(50.0f, 50.0f, 200.0f, 100.0f);
  params.SetBounds(newBounds);
  
  // Set flags to dictate how child sizing behaves if the layout container is resized
  params.SetFlags(Dali::Ui::AbsoluteLayoutFlags::None);
}
```

> Note: The `AbsoluteLayoutFlags` provide control over proportional behavior. If your UI design requires children to maintain aspect ratios or scale with the container, verify that your implementation of `SetFlags` is correctly configured to avoid overlapping or clipping.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/absolute-layout)
