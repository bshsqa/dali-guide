---
id: flex-layout
title: "FlexLayout"
sidebar_label: "FlexLayout"
---
## Introduction to [FlexLayout](./flex-layout.md)

[FlexLayout](./flex-layout.md) is a powerful, Yoga-based layout engine within the DALi framework that arranges its children using the industry-standard CSS Flexbox algorithm. Use [FlexLayout](./flex-layout.md) when you need to create responsive, dynamic user interfaces that automatically distribute space and align components according to flexible constraints, rather than fixed pixel coordinates. It is distinct from other layout variants because it natively handles complex flow behaviors, such as line wrapping and cross-axis alignment, making it the preferred choice for fluid application navigation and adaptive component grids.

## Core Architecture and Integration

The [FlexLayout](./flex-layout.md) component operates as a specialized layout container within the DALi scene graph, managing the transformation and size calculation of its child actors. It utilizes a handle-based memory management model, where `FlexLayout` acts as a smart pointer wrapper around the underlying engine resource, ensuring that the layout configuration persists as long as there is at least one active reference.

### Scene Graph Lifecycle
When you attach a `FlexLayout` to a container, the engine integrates the layout constraints into the DALi [update](./update.md) phase. During each frame, if a property modification triggers a "dirty" state in the layout tree, the Yoga-based engine recalculates the geometry of all affected children before the render phase begins.

## Flex Container Configuration

Configuring a `FlexLayout` requires setting properties that define the global flow of children, such as the axis of arrangement and how items should react to container overflow.

### Setting Flow Direction and Wrapping
The `Direction` and `Wrap` properties dictate the main axis of your layout. `SetDirection` determines whether items stack horizontally or vertically, while `SetWrap` controls whether children should force a new line when the container's main-axis size is exceeded.

> **Note:** Changing the direction or wrap property will trigger a full relayout of all children within the container, which may have a performance impact if executed frequently inside an [animation](./animation.md) loop.

```cpp
using namespace Dali::Ui;

void ConfigureFlexContainer(FlexLayout& layout) {
    // Configure flow: horizontal, wrapping to new lines if needed
    layout.SetDirection(FlexDirection::ROW);
    layout.SetWrap(FlexWrap::WRAP);
    
    // Fluent API usage for chaining configuration
    layout.Direction(FlexDirection::COLUMN)
          .Wrap(FlexWrap::NO_WRAP);
}
```

## Alignment and Justification Logic

This section defines how child actors are positioned along the primary and cross axes. The `JustifyContent` and `AlignItems` properties serve as the primary levers for distributing extra space and positioning items within the layout container.

### JustifyContent vs. AlignItems
`SetJustifyContent` manages the distribution of items along the main axis (e.g., center, space-between, or flex-start), while `SetAlignItems` controls the alignment of all children along the cross-axis.

```cpp
void ConfigureAlignment(FlexLayout& layout) {
    // Distribute children with equal spacing along the main axis
    layout.SetJustifyContent(FlexJustify::SPACE_BETWEEN);
    
    // Align children to the center of the cross-axis
    layout.SetAlignItems(FlexAlign::CENTER);
    
    // For wrapped lines, align the lines themselves within the container
    layout.SetAlignContent(FlexAlign::FLEX_START);
}
```

## Thread Safety and Performance Constraints

While DALi property setters are generally accessible, they must be invoked within the context of the main application thread. Modifications to `[FlexLayout](./flex-layout.md)` properties directly update the internal Yoga configuration; avoid rapid, frame-by-frame updates to these properties to ensure the layout engine maintains a steady frame rate.

### Optimization Best Practices
*   Batch property changes: Use the fluent API setters to update all layout parameters in a single block.
*   Avoid frequent structure changes: Changing the child count or nesting levels in a `[FlexLayout](./flex-layout.md)` is more expensive than adjusting existing properties.
*   Use `FlexLayoutParams` for per-child specific constraints rather than forcing global layout properties.

## API Lifecycle and Memory Management

`[FlexLayout](./flex-layout.md)` instances follow standard DALi reference counting rules. You should manage them via the `New()` factory method and use `DownCast()` when retrieving handles from generic containers.

### Creating and Managing Handles
The `FlexLayoutParams` class is critical for individual child control. While the `[FlexLayout](./flex-layout.md)` class defines the container behavior, you apply `FlexLayoutParams` to children to define how much an individual item should grow or shrink.

```cpp
#include <dali/dali.h>

void CreateFlexContainer() {
    // 1. Create the container layout
    FlexLayout layout = FlexLayout::New();
    layout.SetDirection(FlexDirection::ROW);

    // 2. Configure a child with specific flex parameters
    FlexLayoutParams params = FlexLayoutParams::New();
    params.SetFlexGrow(1.0f);   // Allow child to expand
    params.SetFlexShrink(0.0f); // Prevent child from shrinking
    
    // 3. Applying layout params to an actor (pseudo-code context)
    // actor.SetProperty(FlexLayoutParams::Property::FLEX_GROW, params.GetFlexGrow());
}

void HandleDowncasting(BaseHandle handle) {
    // Safely cast a base handle to a FlexLayout
    FlexLayout layout = FlexLayout::DownCast(handle);
    if (layout) {
        // Safe to use the layout handle
    }
}
```

> **Warning:** Always check the validity of a `DownCast` return value; an invalid `BaseHandle` or a handle pointing to a different object type will result in an empty (uninitialized) `[FlexLayout](./flex-layout.md)` handle.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/flex-layout)
