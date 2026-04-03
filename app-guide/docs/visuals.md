---
id: visuals
title: "visuals"
sidebar_label: "visuals"
---
## Introduction to DALi Visuals

The DALi Visuals framework provides a high-performance, property-driven system for [rendering](./rendering.md) diverse content types—ranging from simple colors to complex 3D meshes—within UI components. By abstracting the underlying graphics operations, Visuals allow developers to define appearance through declarative properties, ensuring efficient resource management and hardware-accelerated [rendering](./rendering.md).

Visuals are the core mechanism for content delivery in the `Dali::Ui::View` architecture. You should use them whenever you need to populate a UI element with graphical content, as they offer optimized pathways for [common](./common.md) UI tasks like image decoding, border drawing, and [text](./text.md) [rendering](./rendering.md).

## Applying Visuals to Actors

Applying a visual involves configuring a set of properties and mapping them to a UI component, typically a `View`. The framework uses a property-map approach, allowing you to define the visual type and its specific attributes in a single structured [object](./object.md).

To apply a visual, you assign the property map to the relevant property of your UI component. DALi handles the creation, staging, and lifecycle of the [rendering](./rendering.md) resources automatically based on the policies defined within the visual's properties.

> Note: While Visuals are the standard for content, ensure you are using the public `Dali::Ui` namespace components to maintain compatibility across all DALi platforms.

## Visual Transformation and [Layout](./layout.md)

Visuals support transformation and layout properties that allow you to precisely position and scale the rendered content within the bounds of its host component. Through the `Dali::Ui::Visual::Property` system, you can control the anchor point, offset, and size constraints of the visual independently of the actor it resides in.

This separation allows for complex visual effects, such as scaling an image to fill a specific area or adding padding around a border, without altering the underlying geometric structure of the host component. Using these properties ensures that the visual remains responsive to layout changes of the parent.

## Visual Sub-Components Overview

DALi provides a comprehensive suite of specialized visual types designed for specific [rendering](./rendering.md) tasks. Each visual is optimized for its particular use case to ensure minimal memory footprint and maximum render speed.

*   **[ColorVisual](./color-visual.md)**: Renders a simple, solid color to a component's quad. → See: `Dali::Ui::ColorVisual`
*   **ImageVisual**: Handles the loading and [rendering](./rendering.md) of image files, including support for various load and release policies. → See: `Dali::Ui::ImageVisual`
*   **[BorderVisual](./border-visual.md)**: Renders a configurable solid-color border around the internal bounds of a component. → See: `Dali::Ui::BorderVisual`
*   **GradientVisual**: Renders a smooth transition between multiple colors with support for various coordinate systems. → See: `Dali::Ui::GradientVisual`
*   **TextVisual**: Optimized for [rendering](./rendering.md) [text](./text.md) strings with style properties. → See: `Dali::Ui::TextVisual`
*   **PrimitiveVisual**: Draws basic 3D shapes like cubes and spheres. → See: `Dali::Ui::PrimitiveVisual`
*   **MeshVisual**: Renders complex 3D geometry using .obj files and associated material files. → See: `Dali::Ui::MeshVisual`

## Common Visual Operations

Managing [visuals](./visuals.md) involves updating their property maps to reflect state changes, such as modifying a color or swapping an image source. Because [visuals](./visuals.md) are managed through properties, you can trigger visual updates using standard property [animation](./animation.md) or transition systems.

### Managing Visual Lifecycle
The `LoadPolicy` and `ReleasePolicy` (found in `Dali::Ui::ImageVisual`) are critical for memory management. Using `LoadPolicy::IMMEDIATE` forces the visual to load resources when it is created, while `LoadPolicy::ATTACHED` defers loading until the visual is actually added to the stage.

```cpp
// Example: Configuring an ImageVisual with explicit policies
Dali::Property::Map visualMap;
visualMap[Dali::Ui::Visual::Property::TYPE] = Dali::Ui::Visual::IMAGE;
visualMap[Dali::Ui::ImageVisual::Property::URL] = "path/to/image.png";
visualMap[Dali::Ui::ImageVisual::Property::LOAD_POLICY] = Dali::Ui::ImageVisual::LoadPolicy::ATTACHED;
visualMap[Dali::Ui::ImageVisual::Property::RELEASE_POLICY] = Dali::Ui::ImageVisual::ReleasePolicy::DETACHED;

// Apply to a View (pseudocode for demonstration)
myView.SetProperty(Dali::Ui::View::Property::BACKGROUND, visualMap);
```

> Warning: Always prefer `LoadPolicy::ATTACHED` for non-critical assets to prevent UI thread blocking during initial application launch or view transitions.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/visuals)
