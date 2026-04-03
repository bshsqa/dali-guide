---
id: border-visual
title: "BorderVisual"
sidebar_label: "BorderVisual"
---
## Introduction to Border Visual

The `BorderVisual` is a specialized [rendering](./rendering.md) component within the DALi framework designed to draw a solid-colored stroke along the internal perimeter of a control's rectangular boundary. It is the preferred choice for UI elements requiring highlighted edges, frames, or decorative borders without the overhead of texture-based assets or custom shader development.

Unlike general-purpose [visuals](./visuals.md), `BorderVisual` provides highly optimized, geometry-based drawing that scales natively with the parent actor's size. It is distinct from the [ColorVisual](./color-visual.md) or ImageVisual as it focuses strictly on stroke width and color attributes, ensuring minimal GPU state changes when [rendering](./rendering.md) UI frames.

→ See: [[ColorVisual](./color-visual.md)]

## Border Visual Property Map

The `BorderVisual` is configured via a `Dali::Property::Map` passed to the visual creator, defining the aesthetic parameters of the stroke. Proper configuration of these properties allows for dynamic styling of borders during runtime.

### Property Configuration

The following table outlines the key properties available within the `Dali::Ui::BorderVisual::Property` namespace:

| Property | Type | Description |
| :--- | :--- | :--- |
| `COLOR` | `Vector4` | The RGBA color of the border. |
| `SIZE` | `float` | The thickness of the border in pixels. |

To apply these, construct a `Property::Map` and pass it to the visual factory.

```cpp
#include <dali/dali.h>
#include <dali/devel-api/adaptor-framework/visual-factory.h>

using namespace Dali;

// Example: Configuring a red border with 5.0f thickness
Property::Map CreateBorderVisual()
{
  Property::Map map;
  map.Insert(Toolkit::Visual::Property::TYPE, Toolkit::Visual::BORDER);
  map.Insert(Toolkit::BorderVisual::Property::COLOR, Vector4(1.0f, 0.0f, 0.0f, 1.0f));
  map.Insert(Toolkit::BorderVisual::Property::SIZE, 5.0f);
  return map;
}
```

> Note: All property values are set as local space units. If the actor's scale is transformed, the border thickness will scale accordingly unless handled via constraints.

## Rendering Pipeline and Internal Architecture

The `[BorderVisual](./border-visual.md)` operates by generating a dedicated set of vertices representing the stroked rectangle. When the property map is processed, DALi creates a specialized geometry primitive that calculates the inner and outer vertices based on the `SIZE` property.

By utilizing internal vertex shaders, the `[BorderVisual](./border-visual.md)` avoids full-quad pixel processing for the center of the shape, effectively treating the interior as transparent. This pipeline optimization ensures that the fragment shader only processes the pixels occupied by the border itself, significantly reducing the fill-rate requirements for complex UI scenes.

## Thread Safety and Lifecycle Management

The `[BorderVisual](./border-visual.md)` follows the standard DALi object lifecycle, where visual properties are defined on the main thread and synchronized with the render thread. While the `Property::Map` can be updated at any time, heavy modifications during animation frames may trigger visual batching recomputations.

*   **Initialization:** Visuals are created via the `VisualFactory`, which returns a `Visual::Base` handle.
*   **Updates:** Use `Visual::Base::SetProperties()` to update the border color or thickness dynamically.
*   **Thread Constraints:** All interactions with the visual's property interface must occur on the Main Event Loop. The render thread handles the subsequent geometry buffer updates internally.

```cpp
// Updating a visual on the fly
void UpdateBorderThickness(Toolkit::Visual::Base visual, float newSize)
{
  Property::Map propertyMap;
  propertyMap.Insert(Toolkit::BorderVisual::Property::SIZE, newSize);
  visual.SetProperties(propertyMap);
}
```

## Integration with Actor Hierarchy

When a `[BorderVisual](./border-visual.md)` is added to an `Actor`, the visual respects the actor's `Size` property. The stroke is drawn inside the quad defined by the actor's dimensions, meaning the border does not extend beyond the actor's established layout bounds.

If you apply layout constraints or transition animations to the host actor, the `[BorderVisual](./border-visual.md)` automatically updates its geometry to match the new quad dimensions during the next frame tick. This integration ensures that the border remains perfectly flush with the actor edges, preventing visual artifacts like gaps or mismatched corners.

## Performance Best Practices

To maintain high frame rates when using multiple `[BorderVisual](./border-visual.md)` instances:

1.  **Batching:** If multiple actors use the same border color and thickness, the DALi rendering engine attempts to batch these into single draw calls. Using identical property sets where possible maximizes this effect.
2.  **Avoid Constant Updates:** While updating properties is thread-safe, frequent updates (every frame) force the recreation of vertex buffers. Prefer using `Animation` objects to animate properties rather than manual `SetProperties` calls in an update loop.
3.  **Geometry Overhead:** Keep the border `SIZE` reasonable. Excessively large borders do not increase geometry count but can impact pixel fill rates on lower-end devices.

```cpp
// Example: Adding to a control
Toolkit::Control myControl = Toolkit::Control::New();
myControl.SetSize(200.0f, 200.0f);

Property::Map map = CreateBorderVisual();
Toolkit::Visual::Base border = Toolkit::VisualFactory::Get().CreateVisual(map);
myControl.RegisterVisual(Toolkit::Control::Property::BACKGROUND, border);

Stage::GetCurrent().Add(myControl);
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/border-visual)
