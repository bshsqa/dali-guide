---
id: absolute-layout
title: "AbsoluteLayout"
sidebar_label: "AbsoluteLayout"
---
## Understanding [AbsoluteLayout](./absolute-layout.md)

The `AbsoluteLayout` component provides a deterministic, coordinate-based positioning system for UI elements. Unlike other layout containers that automate child arrangement, `AbsoluteLayout` delegates full control of a child’s position and size to the developer, making it the ideal choice for static overlays, fixed-position icons, or interfaces requiring pixel-perfect alignment.

This layout is distinct because it ignores [common](./common.md) dynamic flow rules and relies instead on explicit `LayoutRect` values applied to each child via `AbsoluteLayoutParams`. It is best used when your UI design requires static composition where child elements do not shift or resize based on the container's contents.

## Instantiating and Attaching Layouts

To utilize the absolute positioning system, you must instantiate the layout and assign it to your target container. The `New()` factory method is the standard way to create an instance, returning a managed handle to the layout [object](./object.md).

### Creating an [AbsoluteLayout](./absolute-layout.md)
The `New()` method returns a new `AbsoluteLayout` instance, which can then be applied to an `Actor` configured for layout support.

```cpp
#include <dali/ui/absolute-layout.h>

// Create the layout
Dali::Ui::AbsoluteLayout myLayout = Dali::Ui::AbsoluteLayout::New();

// Assume 'container' is a valid Actor instance
// container.SetLayout(myLayout);
```

> Note: `[AbsoluteLayout](./absolute-layout.md)` objects are handle-based; copying them is efficient as they reference the same underlying object.

## Positioning Elements in Absolute Space

Positioning and sizing are achieved using the `AbsoluteLayoutParams` class. Each child element added to an `[AbsoluteLayout](./absolute-layout.md)` container must have an associated `AbsoluteLayoutParams` object that defines its specific bounds within the parent's coordinate space.

### Using AbsoluteLayoutParams
You can set dimensions either globally using `SetBounds` or via specific coordinate/dimension methods like `SetX`, `SetY`, `SetWidth`, and `SetHeight`.

```cpp
#include <dali/ui/absolute-layout.h>

// Create parameters for a child
Dali::Ui::AbsoluteLayoutParams params = Dali::Ui::AbsoluteLayoutParams::New();

// Define position (100, 150) and size (200x50)
params.SetX(100.0f);
params.SetY(150.0f);
params.SetWidth(200.0f);
params.SetHeight(50.0f);

// Apply to a child actor
// childActor.SetLayoutParams(params);
```

> Warning: Ensure that the values provided to these methods are non-negative, as negative dimensions may lead to undefined rendering behavior within the layout container.

## Managing Layout Lifecycle

`[AbsoluteLayout](./absolute-layout.md)` manages its resources automatically through reference counting. As a handle-based class, it follows standard C++ value semantics, and the destructor ensures that internal handles are released correctly when the object goes out of scope.

### Cleanup and Destruction
You do not need to manually delete a layout; simply allow the `[AbsoluteLayout](./absolute-layout.md)` object to go out of scope. The implementation handles the internal release of resources associated with the layout structure.

```cpp
{
    Dali::Ui::AbsoluteLayout layout = Dali::Ui::AbsoluteLayout::New();
    // Layout is active during this scope
} // Layout resource is automatically released here
```

## Type Casting and Safety

When interacting with generic handles (such as those returned by a parent's `GetLayout()` method), you must use the `DownCast` utility to restore the specific `[AbsoluteLayout](./absolute-layout.md)` interface.

### Safe Downcasting
`DownCast` verifies the underlying type of the handle. If the handle is not an `[AbsoluteLayout](./absolute-layout.md)`, the returned handle will be uninitialized (empty).

```cpp
#include <dali/ui/absolute-layout.h>

// Assuming 'someHandle' is a generic BaseHandle retrieved from a container
Dali::Ui::AbsoluteLayout layout = Dali::Ui::AbsoluteLayout::DownCast(someHandle);

if (layout)
{
    // The handle is valid and confirmed to be an AbsoluteLayout
    float currentX = layout.GetX(); // (If available via specific logic)
}
```

## Common Implementation Patterns

Developers often use the assignment operator to pass layout handles across UI builder functions. Because these are handles, assignment is highly performant and does not involve deep copying of the internal layout data.

### Standard Setup Pattern
This example demonstrates a typical initialization pattern, capturing the movement of a layout object to configure a container.

```cpp
Dali::Ui::AbsoluteLayout SetupContainer()
{
    Dali::Ui::AbsoluteLayout layout = Dali::Ui::AbsoluteLayout::New();
    
    // Copy assignment
    Dali::Ui::AbsoluteLayout myLayout;
    myLayout = layout; 
    
    return myLayout;
}

// Moving an instance
Dali::Ui::AbsoluteLayout moveLayout(Dali::Ui::AbsoluteLayout::New());
Dali::Ui::AbsoluteLayout finalLayout = std::move(moveLayout);
```

> See: [Layouts (Parent Module)] for broader context on how these [layouts](./layouts.md) fit into the DALi container hierarchy.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/absolute-layout)
