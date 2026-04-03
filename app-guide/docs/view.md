---
id: view
title: "View (Base UI Object)"
sidebar_label: "View (Base UI Object)"
---
## Introduction to the View Framework

The `Dali::Ui::View` is the primary building block for creating user interfaces within the DALi framework. It serves as the base class for all visual components, encapsulating the complex [rendering](./rendering.md), transformation, and event-handling logic required to display and interact with elements on the screen.

You should use `Dali::Ui::View` when you need to construct a custom UI element that requires its own lifecycle, input handling, or specific visual representation. Unlike raw `Dali::Actor` objects, which are engine-level primitives, `View` provides a higher-level API surface tailored for application developers, ensuring consistent state management and simplified integration into the UI scene graph.

## Working with View Properties

Configuring a `View` involves manipulating its visual and spatial attributes to define how it appears and behaves within the layout. Properties such as position, size, and visibility are handled through a unified interface that ensures updates are synchronized with the [rendering](./rendering.md) engine.

### Setting [Geometry](./geometry.md) and Visibility
To manage the layout of a view, developers adjust its position, size, and visibility state. These properties determine the view's footprint within its parent coordinate system and whether it participates in the render pass.

*   **SetPosition(float x, float y, float z)**: Sets the coordinate position of the view relative to its parent.
*   **SetSize(float width, float height)**: Defines the dimensions of the view in local coordinates.
*   **SetVisible(bool visible)**: Toggles whether the view is rendered to the screen.

```cpp
#include <dali/ui/view.h>

void CreateCustomUI(Dali::Ui::View parentView) {
    auto myView = Dali::Ui::View::New();
    
    // Position the view at the top-left of the parent
    myView.SetPosition(100.0f, 100.0f, 0.0f);
    
    // Set fixed dimensions
    myView.SetSize(200.0f, 50.0f);
    
    // Ensure the view is visible
    myView.SetVisible(true);
    
    parentView.Add(myView);
}
```

> **Note**: Changes to properties like size and position are processed at the start of the next frame. Frequent updates within a single frame are optimized by the underlying engine.

## Handling User Interaction

Interactivity in DALi is managed through signal-based event handling. `Dali::Ui::View` provides standard signals that allow your application to react to user inputs, such as touch events or focus state changes, without needing to process raw input buffers.

### Connecting to Touch Events
The `TouchedSignal` allows you to define callbacks that execute when a user interacts with the view surface.

*   **TouchedSignal()**: Returns a signal object that developers can connect to for receiving touch data (e.g., press, move, release).

```cpp
#include <dali/ui/view.h>

bool OnTouch(Dali::Ui::View& view, const Dali::TouchData& event) {
    // Handle the touch event logic here
    return true; // Return true to consume the event
}

void SetupInteraction(Dali::Ui::View view) {
    view.TouchedSignal().Connect(&OnTouch);
}
```

## Sub-Components Overview

The `View` framework includes several specialized components designed for common UI tasks. These classes extend `Dali::Ui::View` to provide pre-built rendering and logic.

*   **Image View**: Displays raster or vector images with support for scaling and tiling. → See: [Image-View]
*   **Label**: Handles text rendering, including font styling, alignment, and multi-line support. → See: [Label]
*   **Scroll View**: Provides a container that enables panning and content clipping for overflow layouts. → See: [Scroll-View]
*   **Input Field**: A specialized view for text entry, including virtual keyboard integration. → See: [Input-Field]
*   **Animated Image View**: Optimized for rendering sequences of frames or animated file formats. → See: [Animated-Image-View]
*   **Lottie Animation View**: A specialized view for rendering high-quality, vector-based Lottie animations. → See: [Lottie-Animation-View]
*   **Layout**: Provides programmatic positioning containers for managing children. → See: [Layout]

## Managing View Hierarchy

Organizing your UI into a tree structure is essential for performance and layout management. `Dali::Ui::View` acts as a container, allowing you to nest views to create complex, manageable UI scenes.

### Adding and Removing Views
The hierarchy is manipulated by adding a child view to a parent view. When a parent view's properties (like position or visibility) change, those changes are implicitly propagated to its children.

*   **Add(View child)**: Registers a view as a child, positioning it within the parent's coordinate space.
*   **Remove(View child)**: Removes the association between the parent and child, effectively removing the child from the scene.

```cpp
void BuildScene(Dali::Ui::View root) {
    auto container = Dali::Ui::View::New();
    auto child = Dali::Ui::View::New();
    
    container.Add(child);
    root.Add(container);
    
    // To remove the child later:
    // container.Remove(child);
}
```

> **Warning**: Avoid creating circular parent-child relationships, as this will prevent the view tree from [rendering](./rendering.md) correctly and may cause memory leaks. Always ensure that a view is removed from its parent before destroying the view [object](./object.md).

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/view)
