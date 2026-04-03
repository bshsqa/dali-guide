---
id: layouts
title: "layouts"
sidebar_label: "layouts"
---
## Introduction to DALi Layouts

The DALi layout system provides a structured mechanism for managing the spatial arrangement and sizing of UI elements within an application. By utilizing dedicated layout managers, developers can move away from manual coordinate management to a responsive, policy-driven approach that adapts to screen sizes and dynamic content.

Layouts are essential when building interfaces that must maintain structural integrity across various device resolutions or handle fluid UI states. Unlike manual positioning, this system encapsulates the logic required to calculate child positions and dimensions, significantly reducing boilerplate code and preventing layout errors during hierarchy updates.

## [Layout](./layout.md) System Concepts

The layout architecture is built on the interaction between a container and its assigned layout manager. The system separates the "where/how" of the positioning logic from the visual components themselves, allowing the same UI hierarchy to behave differently simply by swapping the layout manager.

### [Layout](./layout.md) Controllers and Parameters
[Layout](./layout.md) managers act as controllers that interpret the size of a container and the constraints of its children. Each child in a layout is associated with a specific set of parameters, which dictate how that individual child participates in the parent's layout policy.

> Note: While the layout process is managed by the framework, developers are responsible for ensuring that the parent container has sufficient dimensions to accommodate the calculated child [layouts](./layouts.md).

## Sub-Components Overview

The layout system offers several specialized engines, each tailored for distinct UI design patterns. Selecting the appropriate engine allows for the most efficient path to achieving a desired design.

*   **[AbsoluteLayout](./absolute-layout.md)**: Positions children at explicit, fixed coordinates within the parent container. → See: [[AbsoluteLayout](./absolute-layout.md)]
*   **[FlexLayout](./flex-layout.md)**: Organizes items in a flexible box model, supporting alignment and distribution based on available space. → See: [[FlexLayout](./flex-layout.md)]
*   **[GridLayout](./grid-layout.md)**: Arranges items into rows and columns of equal or proportional size. → See: [[GridLayout](./grid-layout.md)]
*   **StackLayout**: Layers items on top of one another along the Z-axis. → See: [StackLayout]

## Applying Layouts to Actors

To implement a layout, you must associate a layout manager with a container and define the configuration parameters for each child actor. This two-tier configuration ensures both global structure and granular control over individual elements.

### Configuring [AbsoluteLayout](./absolute-layout.md)
The `AbsoluteLayout` is used when you need precise control over the pixel-perfect position of children. It relies on the `AbsoluteLayoutParams` class to store the bounds for each child.

#### Using AbsoluteLayoutParams
The `AbsoluteLayoutParams` allows you to set the X, Y, width, and height of a child actor within the parent layout.

**Code Example:**

```cpp
#include <dali/ui/layouts/absolute-layout.h>

void CreateAbsoluteLayout(Dali::Actor parent)
{
    // Create the layout manager
    auto layout = Dali::Ui::AbsoluteLayout::New();

    // Create parameters for a child
    auto params = Dali::Ui::AbsoluteLayoutParams::New();
    params.SetX(50.0f);
    params.SetY(100.0f);
    params.SetWidth(200.0f);
    params.SetHeight(150.0f);

    // Note: Applying these params to an actor is handled 
    // via the actor's layout integration API.
}
```

> Warning: `[AbsoluteLayout](./absolute-layout.md)` does not automatically resize children when the parent container changes size. Ensure your application logic handles re-calculation if the parent viewport scales.

## Best Practices for Responsive UI

Achieving a high-performance, responsive UI requires choosing the most appropriate layout engine for the task. Avoid over-nesting [layouts](./layouts.md), as this increases the complexity of the measurement pass and can impact frame timing.

*   **Prefer [FlexLayout](./flex-layout.md) for flows**: Use flex-based [layouts](./layouts.md) for lists, navigation bars, and dynamic content that changes size frequently.
*   **Use [AbsoluteLayout](./absolute-layout.md) sparingly**: Reserve absolute positioning for fixed UI overlays or decorations where relative positioning is mathematically impractical.
*   **Constraint testing**: Always validate your layout logic against different screen aspect ratios to ensure no content clipping occurs.
*   **Performance**: Minimize the number of times you trigger a relayout by batching property updates to child actors before the layout pass executes.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/layouts)
