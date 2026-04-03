---
id: layouts
title: "layouts"
sidebar_label: "layouts"
---
## Introduction to DALi Layouts

The DALi layout system is a robust, declarative framework designed to manage the positioning and sizing of UI elements within the DALi engine. It decouples the visual representation of a view from its spatial arrangement, enabling complex UI compositions that remain performant and responsive to dynamic changes in screen geometry.

Developers should utilize the layout system when UI requirements exceed simple hierarchical transformations. By offloading coordinate calculations to specialized `LayoutManager` implementations, the framework ensures consistent behavior across diverse device resolutions and aspect ratios, making it the primary choice for professional-grade application interfaces.

## System Architecture and Internal Flow

The DALi layout architecture relies on a clear separation of concerns between state, logic, and execution layers. The process begins with the `LayoutController`, which monitors dirty states within the view tree and triggers the layout pass.

### LayoutManager and Integration Flow
The `LayoutManager` acts as the engine's core processing unit for a specific layout strategy. It provides the implementation of `Measure` and `ArrangeChildren` methods, which are invoked by the internal layout pipeline.
*   `Measure`: Calculates the desired size of a view and its subtree based on provided constraints.
*   `ArrangeChildren`: Positions child views within the allocated `LayoutRect` bounds.

> Note: [Layout](./layout.md) managers operate within the integration-api layer. When creating custom [layouts](./layouts.md), you must ensure that your `LayoutManager` subclass effectively overrides these methods to prevent layout thrashing and unexpected visual clipping.

## Sub-Components Overview

The layout family provides specialized engines to handle different spatial requirements. Each engine is optimized for specific structural patterns.

*   **[AbsoluteLayout](./absolute-layout.md)**: A layout engine that positions children at specific, fixed coordinates defined by the developer. → See: [AbsoluteLayout](absolute-layout.md)
*   **[FlexLayout](./flex-layout.md)**: A flexible box model that aligns and justifies content based on dynamic constraints. → See: [FlexLayout](flex-layout.md)
*   **[GridLayout](./grid-layout.md)**: A grid-based system for arranging components in rows and columns. → See: [GridLayout](grid-layout.md)

## Integration API and Threading Model

The `Integration::AbsoluteLayoutImpl` and `Integration::AbsoluteLayoutManager` are designed to interact directly with the DALi core [rendering](./rendering.md) loop. 

### Threading Considerations
[Layout](./layout.md) calculations are strictly bound to the main application thread to ensure data consistency with the scene graph. While `LayoutManager` methods might be invoked frequently during dynamic UI updates, you must avoid long-running blocking operations within these methods to maintain the 60fps frame budget.

## [Layout](./layout.md) Cycle: Measurement and Arrangement

The layout cycle follows a two-pass approach: a measurement pass (top-down) and an arrangement pass (top-down/bottom-up).

### Implementation using AbsoluteLayoutManager
The `AbsoluteLayoutManager` facilitates this process by iterating through child nodes and applying the `AbsoluteLayoutParams` assigned to them.

```cpp
// Example: Implementing a custom manager flow
using namespace Dali::Ui::Integration;

class MyCustomManager : public AbsoluteLayoutManager {
public:
    MeasuredSize Measure(ViewImpl *view, float widthConstraint, float heightConstraint) override {
        // Calculate requirements based on child constraints
        return AbsoluteLayoutManager::Measure(view, widthConstraint, heightConstraint);
    }

    MeasuredSize ArrangeChildren(ViewImpl *view, const LayoutRect &bounds) override {
        // Perform final placement logic
        return AbsoluteLayoutManager::ArrangeChildren(view, bounds);
    }
};
```

## Configuration and Parameter Management

Layout configurations are managed through specialized parameter objects that act as data containers for the layout engine.

### Using AbsoluteLayoutParams
`AbsoluteLayoutParams` allows developers to define the exact geometry of a child view within an `[AbsoluteLayout](./absolute-layout.md)` container. 

*   **SetBounds**: Defines the exact `LayoutRect` (x, y, width, height) for the child.
*   **SetX/SetY/SetWidth/SetHeight**: Provides granular control over individual coordinate or dimension properties.

```cpp
#include <dali/ui/absolute-layout.h>

void SetupAbsoluteView() {
    using namespace Dali::Ui;
    
    // Create the layout
    AbsoluteLayout layout = AbsoluteLayout::New();
    
    // Create parameters for a child
    AbsoluteLayoutParams params = AbsoluteLayoutParams::New();
    params.SetX(50.0f);
    params.SetY(100.0f);
    params.SetWidth(200.0f);
    params.SetHeight(150.0f);
    
    // Apply parameters to a target view (assume 'childView' is a valid Handle)
    // childView.SetLayoutParams(params);
}
```

> Warning: Always ensure that `LayoutParams` objects are correctly associated with their parent view's specific layout type. Attempting to assign `AbsoluteLayoutParams` to a view managed by a `[GridLayout](./grid-layout.md)` will result in undefined behavior during the layout pass.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/layouts)
