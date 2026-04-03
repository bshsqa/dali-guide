---
id: visuals
title: "visuals"
sidebar_label: "visuals"
---
## Introduction to DALi Visuals

The DALi Visuals framework provides a high-performance, abstraction-based approach for [rendering](./rendering.md) graphical content within UI controls. By decoupling the visual representation (e.g., [images](./images.md), colors, borders) from the control logic, developers can create complex, resource-efficient UI components that leverage the engine's optimized [rendering](./rendering.md) pipeline.

The framework is divided into three tiers: **Public API** for stable, high-level features; **Devel API** for advanced, evolving functionality; and **Integration API** for low-level component implementation. Use Visuals whenever you need to render stylized content within a `View` or custom control, as they are specifically engineered for efficient memory usage and hardware-accelerated drawing.

## Visual Architecture and Lifecycle

Visuals in DALi operate as lightweight, data-driven entities managed by the control's renderer. Instead of creating heavy Actor-based scene graph objects for every visual element, the framework uses property-based configuration to define appearance, which is then processed by the underlying DALi engine.

The lifecycle begins with the creation of a visual via a property map and ends when the visual is detached from the control or the control is destroyed. The engine handles the resource loading—such as textures for [images](./images.md) or vector data for Lottie animations—asynchronously, ensuring that the main UI thread remains fluid during complex [rendering](./rendering.md) tasks.

## Visual Transformations and Thread Safety

Visuals support coordinate transformations through the `DevelVisual::Transform` [utility](./utility.md), allowing for precise control over size, position, and anchor points without affecting the parent control's layout geometry. This separation ensures that UI layout remains predictable while allowing for high-frequency visual adjustments.

> Warning: Property updates to [visuals](./visuals.md) are generally thread-safe if performed via the standard DALi Property system. However, direct manipulation of underlying implementation objects from non-main threads must be avoided to prevent race conditions within the render tree.

For complex animations, developers should utilize the `Dali::Ui::DevelAnimatedVectorImageVisual::DynamicPropertyInfo` structure to bind specific key paths to callback mechanisms.

```cpp
// Example: Setting up a dynamic property for a Vector Visual
Dali::Ui::DevelAnimatedVectorImageVisual::DynamicPropertyInfo info;
info.id = 100;
info.keyPath = "Layer 1/Shape/Color";
info.property = Dali::Property::COLOR;
// The callback is triggered when the visual update reaches the specified key path
```

## Visuals Sub-Components Overview

The framework provides a suite of specialized visual types designed for distinct rendering tasks.

*   **Animated Vector Image Visual**: Renders resolution-independent vector animations, such as Lottie files, with support for dynamic property manipulation. → See: [AnimatedVectorImageVisual]
*   **Animated Image Visual**: Handles multi-frame image formats, providing control over playback states and frame sequences. → See: [AnimatedImageVisual]
*   **Border Visual**: Draws a solid color frame within the control's bounds, allowing for custom thickness and color property configuration. → See: [BorderVisual]
*   **Color Visual**: The simplest visual type, used to render a solid, flat color or a gradient to a control's quad. → See: [ColorVisual]

> Note: For advanced arc-based rendering, consult the `DevelArcVisual` properties, which allow for customizable cap styles via `Dali::Ui::DevelArcVisual::Cap::Type`.

## Custom Shaders and Integration

For requirements beyond standard visuals, the framework exposes an integration layer that allows for custom rendering logic. By implementing a custom view using `Dali::Ui::Integration::ImageViewImpl` or similar classes, developers can inject specialized shaders or handle raw vertex data while remaining within the optimized Visuals framework.

### Custom Property Integration

When building complex controls, you may need to define internal properties that the engine can animate. Use the provided `Property` structs to define these hooks, ensuring they conform to the existing DALi property system.

```cpp
// Example: Accessing standard View properties for custom integration
enum MyCustomViewProperties
{
    PROPERTY_COLOR = Dali::Ui::View::Property::COLOR,
    PROPERTY_LABEL = Dali::Ui::Integration::LabelImpl::Property::TEXT
};
```

> Warning: When implementing custom `Integration::Impl` classes, ensure that you correctly handle the registration of properties to avoid memory leaks or notification failures during the property-set lifecycle. Always verify that your implementation satisfies the `Dali::Ui::Integration` API requirements for property enumeration.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/visuals)
