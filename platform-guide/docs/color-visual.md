---
id: color-visual
title: "ColorVisual"
sidebar_label: "ColorVisual"
---
## Introduction to [ColorVisual](./color-visual.md)

The `ColorVisual` is the most lightweight and performant mechanism in the DALi framework for [rendering](./rendering.md) solid color regions within the scene graph. It is designed to render an opaque or translucent color quad directly to an actor’s geometry, bypassing the overhead associated with texture loading, image decoding, or complex shader state management required by other visual types.

You should use `ColorVisual` whenever you need a solid background, a UI primitive separator, or a decorative rectangular block. It is significantly more memory-efficient than using an `ImageVisual` with a single-pixel white texture, as it avoids texture memory allocation entirely. → See: [Visuals]

## Property Configuration

`ColorVisual` is configured via a `Property::Map` passed to the `VisualFactory`. These properties define the fundamental appearance of the visual, specifically targeting the color value and blending behavior.

### Property Map Definition

To instantiate a `ColorVisual`, you must define the `Visual::Property::TYPE` as `Visual::COLOR` and provide the specific `ColorVisual::Property` keys to define the look.

The primary property is `ColorVisual::Property::MIX_COLOR`, which accepts a `Vector4` representing the RGBA values of the color. 

```cpp
#include <dali/dali.h>
#include <dali/devel-api/adaptor-framework/visual-factory.h>

// Create a property map for a semi-transparent blue ColorVisual
Dali::Property::Map colorVisualMap;
colorVisualMap.Insert(Dali::Toolkit::Visual::Property::TYPE, Dali::Toolkit::Visual::COLOR);
colorVisualMap.Insert(Dali::Toolkit::Visual::Property::MIX_COLOR, Dali::Vector4(0.0f, 0.0f, 1.0f, 0.5f));

// Apply this to an actor using the VisualFactory
Dali::Toolkit::Visual::Base visual = Dali::Toolkit::VisualFactory::Get().CreateVisual(colorVisualMap);
```

> Note: If `MIX_COLOR` is not provided, the visual defaults to white (`1.0, 1.0, 1.0, 1.0`).

## Visual Lifecycle and Lifecycle Management

The `[ColorVisual](./color-visual.md)` lifecycle is managed by the DALi `Visual::Base` handle, which tracks the visual's attachment to an `Actor` and its visibility state within the scene graph. When a `[ColorVisual](./color-visual.md)` is added to an actor, it registers its geometry data with the renderer; when removed or hidden, resources are reclaimed.

### Attachment and Resource Cleanup

The visual lifecycle is tied to the `[Renderer](./renderer.md)` lifecycle. When a `[ColorVisual](./color-visual.md)` is assigned to a control using `control.RegisterVisual()`, the framework generates a simple geometry primitive. There is no external file resource to load, making the initialization near-instantaneous.

```cpp
void SetupVisual(Dali::Toolkit::Control control)
{
    Dali::Property::Map map;
    map.Insert(Dali::Toolkit::Visual::Property::TYPE, Dali::Toolkit::Visual::COLOR);
    map.Insert(Dali::Toolkit::Visual::Property::MIX_COLOR, Dali::Color::RED);

    // Registering the visual manages its lifecycle relative to the control
    control.RegisterVisual(Dali::Toolkit::Control::Property::BACKGROUND, map);
}
```

## Rendering Pipeline Integration

`[ColorVisual](./color-visual.md)` optimizes the rendering pipeline by utilizing a dedicated shader path that draws directly to the frame buffer using constant color buffers. By avoiding texture sampling, it reduces the number of operations in the fragment shader and lowers pressure on the GPU memory bandwidth.

### Blend Mode Handling

The `[ColorVisual](./color-visual.md)` automatically detects alpha values in the `MIX_COLOR`. If the alpha component is less than 1.0, the renderer sets the appropriate blending factors to ensure correct visual output. This is handled internally by the `[Renderer](./renderer.md)` state management, ensuring that solid color fills are batch-optimized whenever possible.

## Thread Safety and Data Synchronization

DALi follows a strict thread-separation model: the Main Thread handles application logic and property updates, while the Render Thread executes draw calls. `[ColorVisual](./color-visual.md)` properties updated via `Property::Map` on the Main Thread are synchronized to the Render Thread via the `RenderTask` system.

> Warning: While property updates are thread-safe, rapid, consecutive updates to `MIX_COLOR` in a tight loop should be avoided to prevent overwhelming the synchronization queue between the Main and Render threads.

## Best Practices for Performance

To maximize performance, use `[ColorVisual](./color-visual.md)` for all solid-colored shapes rather than alternative visual types. Because it is highly optimized, it contributes minimal overhead to hit-testing and layout calculations.

### Minimizing Overhead

1. **Avoid Overdraw**: Use solid opaque colors (`alpha = 1.0`) whenever transparency is not required to allow the GPU to perform early-z testing.
2. **Reuse Visuals**: If multiple controls share the same color, you may share the property map or even the same visual instance if the control layout allows.
3. **Property Updates**: When animating color transitions, use DALi's built-in `Animation` class rather than manually updating properties frame-by-frame, as this allows the animation to be processed efficiently within the rendering pipeline.

```cpp
// Efficient color animation
Dali::Animation animation = Dali::Animation::New(1.0f);
animation.AnimateTo(Dali::Property(myControl, Dali::Toolkit::Control::Property::BACKGROUND), Dali::Color::GREEN);
animation.Play();
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/color-visual)
