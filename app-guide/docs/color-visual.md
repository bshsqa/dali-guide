---
id: color-visual
title: "ColorVisual"
sidebar_label: "ColorVisual"
---
## Getting Started with [ColorVisual](./color-visual.md)

The `ColorVisual` is the most lightweight and performant primitive for [rendering](./rendering.md) solid, uniform colors within a DALi `Control`. It is designed specifically for scenarios where you need to draw backgrounds, solid rectangular UI elements, or simple placeholders without the overhead of texture loading or complex shaders.

You should use `ColorVisual` whenever your design requires a simple color fill, as it avoids the GPU memory cost associated with `ImageVisual` or `GradientVisual`. By leveraging this visual, you ensure your UI remains responsive and memory-efficient even when [rendering](./rendering.md) many colored elements across the screen.

→ See: [ImageVisual], [GradientVisual]

## Configuring Visual Properties

`ColorVisual` is configured via a property map, allowing you to define the visual's appearance using the `Dali::Ui::ColorVisual::Property` enumeration. The primary property is the color itself, which supports standard RGBA values.

### Property Definitions
To define a `ColorVisual`, you populate a `Property::Map` using the keys provided in the `Dali::Ui::ColorVisual::Property` namespace. The `MIX_COLOR` property expects a `Vector4` representing the red, green, blue, and alpha channels (ranging from 0.0 to 1.0).

```cpp
#include <dali/dali.h>
#include <dali-toolkit/dali-toolkit.h>

// Create a Property Map for a semi-transparent blue visual
Dali::Property::Map CreateBlueVisualMap()
{
  Dali::Property::Map propertyMap;
  propertyMap.Insert(Dali::Toolkit::Visual::Property::TYPE, Dali::Toolkit::Visual::COLOR);
  propertyMap.Insert(Dali::Toolkit::ColorVisual::Property::MIX_COLOR, Dali::Vector4(0.0f, 0.0f, 1.0f, 0.5f));
  return propertyMap;
}
```

> Note: The Alpha channel in `MIX_COLOR` determines the opacity of the visual. If you set it to 0.0, the visual will be completely transparent, regardless of the RGB values.

## Integration with Control Objects

To display a `[ColorVisual](./color-visual.md)`, you must register it with a `Control` using the `Visual::Property::Add` method. This links the visual configuration to a specific visual index, which the control then manages during its lifecycle.

### Attaching the Visual
The workflow involves creating the property map, adding it to the control, and optionally setting the visual as the background of the control.

```cpp
void SetupControl(Dali::Toolkit::Control control)
{
  // Define the visual property map
  Dali::Property::Map colorMap;
  colorMap.Insert(Dali::Toolkit::Visual::Property::TYPE, Dali::Toolkit::Visual::COLOR);
  colorMap.Insert(Dali::Toolkit::ColorVisual::Property::MIX_COLOR, Dali::Vector4(1.0f, 0.0f, 0.0f, 1.0f));

  // Add the visual to the control with a specific index
  const int VISUAL_INDEX = 100;
  control.AddVisual(VISUAL_INDEX, colorMap);
}
```

## Dynamic Color Updates

`[ColorVisual](./color-visual.md)` properties can be updated at runtime, making it ideal for UI elements that respond to user interaction or state changes. You can modify the color of an existing visual by updating the property map associated with the visual index.

### Modifying Color at Runtime
By accessing the control's visual properties, you can animate transitions or perform instant color swaps without re-instantiating the visual.

```cpp
void UpdateVisualColor(Dali::Toolkit::Control control, const Dali::Vector4& newColor)
{
  const int VISUAL_INDEX = 100;
  
  Dali::Property::Map propertyMap;
  propertyMap.Insert(Dali::Toolkit::ColorVisual::Property::MIX_COLOR, newColor);
  
  // Apply the updated property to the existing visual
  control.UpdateVisual(VISUAL_INDEX, propertyMap);
}
```

> Warning: Frequent updates to visual properties within a single frame should be avoided to prevent redundant layout calculations. Use animation properties if you require smooth color transitions over time.

## Best Practices for Performance

To maintain high frame rates, treat `[ColorVisual](./color-visual.md)` as a static resource whenever possible. Since the visual is just a set of properties, it is extremely efficient; however, constant re-addition of visuals can be expensive.

### Efficient Usage Patterns
*   **Reuse Maps:** If you use the same color for multiple controls, define the `Property::Map` once and reuse it.
*   **Avoid Overdraw:** Even though `[ColorVisual](./color-visual.md)` is simple, avoid layering many semi-transparent `[ColorVisual](./color-visual.md)` objects on top of each other, as this increases the fill rate burden on the GPU.
*   **Static Defaults:** Set the color once during the control's initialization phase rather than updating it every frame. If dynamic changes are needed, ensure they are triggered by [events](./events.md) (e.g., button press) rather than every tick of a timer.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/color-visual)
