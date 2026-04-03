---
id: flex-layout
title: "FlexLayout"
sidebar_label: "FlexLayout"
---
## Getting Started with [FlexLayout](./flex-layout.md)

The `FlexLayout` class is a powerful layout manager that implements the CSS Flexbox algorithm to arrange child actors within a container. Use `FlexLayout` when you need a responsive, one-dimensional layout that can adapt to changing screen sizes, content requirements, or dynamic item additions without hardcoding specific coordinates.

To initialize a `FlexLayout`, use the static `New()` method. Once created, you can apply it to a container actor to begin managing its children.

```cpp
#include <dali-toolkit/dali-toolkit.h>

using namespace Dali::Ui;

// Creating and applying a FlexLayout
FlexLayout layout = FlexLayout::New();
layout.SetDirection(FlexDirection::COLUMN);

// Assuming 'container' is a valid Actor instance
container.SetLayout(layout);
```

## Defining Layout Direction and Wrapping

The `Direction` and `Wrap` properties dictate the primary axis of your layout and how the flexbox handles items that exceed the available container space. By configuring these, you define whether your items flow in a row or column, and whether they should "stack" into new lines if they become too large.

### FlexDirection and FlexWrap
*   **SetDirection(FlexDirection direction)**: Sets the orientation of the main axis (e.g., `COLUMN` or `ROW`).
*   **SetWrap(FlexWrap wrap)**: Determines if items wrap to multiple lines (`WRAP`) or remain in a single line (`NO_WRAP`).

> Note: Use the fluent interface variants (e.g., `Direction(FlexDirection::ROW)`) to chain property configurations during initialization for cleaner code.

```cpp
FlexLayout layout = FlexLayout::New();
layout.Direction(FlexDirection::ROW)
      .Wrap(FlexWrap::WRAP);
```

## Aligning and Justifying Items

Alignment properties manage the distribution of space along the main and cross axes. While `JustifyContent` controls the spacing of items along the main axis, `AlignItems` and `AlignContent` manage how items are positioned perpendicular to the main flow.

### Justification and Alignment
*   **SetJustifyContent(FlexJustify justify)**: Controls distribution along the main axis.
*   **SetAlignItems(FlexAlign align)**: Sets the default alignment for items along the cross axis.
*   **SetAlignContent(FlexAlign align)**: Defines alignment for multiple lines of content when wrapping is enabled.

```cpp
// Center content horizontally and vertically
FlexLayout layout = FlexLayout::New();
layout.SetJustifyContent(FlexJustify::CENTER);
layout.SetAlignItems(FlexAlign::CENTER);
layout.SetAlignContent(FlexAlign::CENTER);
```

## Managing Layout Constraints

Individual children within a `[FlexLayout](./flex-layout.md)` can be customized using `FlexLayoutParams`. This class allows you to override the default layout behavior for specific actors, enabling fine-grained control over how they grow, shrink, or align themselves within the parent container.

### Applying Child-Specific Parameters
*   **SetFlexGrow(float grow)**: Determines how much an item should expand to fill available space.
*   **SetFlexShrink(float shrink)**: Determines how much an item should shrink if space is constrained.
*   **SetFlexBasis(float basis)**: Sets the base size before any grow or shrink calculations are applied.
*   **SetAlignSelf(FlexAlign align)**: Allows an individual child to override the `AlignItems` property of the `[FlexLayout](./flex-layout.md)`.

```cpp
// Configuring a specific child's layout behavior
FlexLayoutParams params = FlexLayoutParams::New();
params.SetFlexGrow(1.0f);
params.SetFlexShrink(0.0f);
params.SetFlexBasis(100.0f);

// Apply these parameters to a child actor
childActor.SetLayoutParams(params);
```

## FlexLayout Best Practices

To ensure optimal performance and maintainable UI hierarchies, keep the following strategies in mind:

1.  **Keep Nesting Shallow**: While `[FlexLayout](./flex-layout.md)` supports nested containers, excessive nesting can increase layout calculation overhead. Only nest layouts when a simple configuration cannot satisfy the design.
2.  **Use `FlexBasis` for Predictability**: When dealing with fixed-size components, set an explicit `FlexBasis` to prevent unexpected resizing behavior during layout transitions.
3.  **Conflict Resolution**: If an item does not appear as expected, check if `AlignSelf` is conflicting with the parent's `AlignItems` setting.

## API Reference Summary

### FlexLayout Enumerations and Methods
*   `New()`: Creates a new `[FlexLayout](./flex-layout.md)` instance.
*   `SetDirection(FlexDirection)` / `GetDirection()`: Manage main axis flow.
*   `SetWrap(FlexWrap)` / `GetWrap()`: Manage wrapping behavior.
*   `SetJustifyContent(FlexJustify)` / `GetJustifyContent()`: Manage main axis alignment.
*   `SetAlignItems(FlexAlign)` / `GetAlignItems()`: Manage cross axis alignment.
*   `SetAlignContent(FlexAlign)` / `GetAlignContent()`: Manage multi-line cross axis alignment.

### FlexLayoutParams Methods
*   `New()`: Creates a new `FlexLayoutParams` instance.
*   `SetFlexGrow(float)` / `GetFlexGrow()`: Grow factor (default is 0).
*   `SetFlexShrink(float)` / `GetFlexShrink()`: Shrink factor (default is 1).
*   `SetFlexBasis(float)` / `GetFlexBasis()`: Initial size along main axis.
*   `SetAlignSelf(FlexAlign)` / `GetAlignSelf()`: Individual cross-axis alignment.

→ See: [Layouts] (Parent module)

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/flex-layout)
