---
id: mask-effect
title: "MaskEffect"
sidebar_label: "MaskEffect"
---
## Introduction to [MaskEffect](./mask-effect.md)

`MaskEffect` is a specialized implementation of the `RenderEffect` interface designed to apply alpha-channel stencil operations to a `Dali::Ui::View`. By leveraging an auxiliary `View` as a mask source, it enables non-rectangular clipping, complex shape masking, and dynamic transparency effects within the DALi [rendering](./rendering.md) pipeline.

You should use `MaskEffect` when you need to constrain the visibility of an actor based on the alpha channel of another actor (the mask). Unlike simpler clipping techniques, `MaskEffect` provides a flexible, node-based approach to masking that allows the mask itself to be animated, scaled, or transformed independently of the target.

## Masking Architecture and Lifecycle

The `MaskEffect` operates by injecting a specialized shader pass into the [rendering](./rendering.md) graph that utilizes the pixel data from the provided mask view. The lifecycle begins upon instantiation and ends when the effect is manually cleared or the parent `View` is destroyed.

To initialize an effect, you provide a mask `View` that defines the shape. DALi manages the underlying GPU texture resources required for the mask; however, the developer is responsible for the lifecycle of the mask view itself.

```cpp
// Example: Creating a basic MaskEffect
Dali::Ui::View targetView = CreateTargetView();
Dali::Ui::View maskView = CreateMaskView();

// Instantiate the effect with default parameters
Dali::Ui::MaskEffect maskEffect = Dali::Ui::MaskEffect::New(maskView);

// Attach the effect to the target view
targetView.AddEffect(maskEffect);
```

## Configuring Mask Modes

The `MaskMode` enumeration dictates how the pixel data from the mask view interacts with the target view's color and alpha channels. This configuration is determined at construction time and ensures the GPU pipeline correctly processes the alpha stencil.

> Note: `MaskMode` constants determine the mathematical blending operation performed by the shader. Selecting an inappropriate mode may lead to inverted masking or unexpected transparency artifacts.

```cpp
// Example: Creating a MaskEffect with explicit mode and transformation
Dali::Ui::MaskEffect maskEffect = Dali::Ui::MaskEffect::New(
    maskView, 
    Dali::Ui::MaskEffect::MaskMode::Alpha, 
    Dali::Vector2(0.0f, 0.0f), 
    Dali::Vector2(1.0f, 1.0f)
);
```

## Target and Source Mask Management

The `[MaskEffect](./mask-effect.md)` provides granular control over the rendering frequency of the mask imagery via `SetTargetMaskOnce` and `SetSourceMaskOnce`. These methods are critical for performance optimization in scenes with static mask requirements.

### SetTargetMaskOnce and SetSourceMaskOnce

These methods control whether the engine should cache the rendered output of the mask/target or re-render them every frame. Setting these to `true` is highly recommended for static assets to reduce GPU draw calls.

*   **SetTargetMaskOnce(bool)**: If set to `true`, the target is captured in a single pass.
*   **SetSourceMaskOnce(bool)**: If set to `true`, the mask source view is rendered once and cached for subsequent frames.

```cpp
// Example: Optimizing a static mask
Dali::Ui::MaskEffect maskEffect = Dali::Ui::MaskEffect::New(maskView);

// Cache the mask and target after the first frame to save CPU/GPU cycles
maskEffect.SetTargetMaskOnce(true);
maskEffect.SetSourceMaskOnce(true);

targetView.AddEffect(maskEffect);
```

## Performance and Threading Considerations

When manipulating `[MaskEffect](./mask-effect.md)` properties, it is important to remember that these effects interact with the render graph on the rendering thread. While property setters are generally thread-safe within the context of the DALi event queue, excessive updates to mask transformations can trigger frequent buffer re-allocations.

> Warning: Enabling `SetTargetMaskOnce(false)` (the default) forces the renderer to perform expensive texture operations every frame. Only use dynamic masking (setting `Once` to `false`) when the mask view requires animation or real-time content changes.

## Best Practices for Visual Fidelity

To achieve professional-grade alpha-compositing, ensure that the resolution of your mask view matches the intended visual output of the target view as closely as possible. Large scale mismatches between the mask and the target view can introduce aliasing or pixelation in the mask edges.

- **Use caching**: Always prioritize `SetSourceMaskOnce(true)` for static images or UI shapes to prevent per-frame overhead.
- **Cleanup**: Because `[MaskEffect](./mask-effect.md)` is a resource-intensive component, ensure you manually clear the effect from the view when it is no longer required to free up GPU memory.
- **Alignment**: Ensure that the `maskPosition` and `maskScale` provided during `New()` align with the bounding box of your target view to avoid unexpected offsets in the clipping area.

→ See: [RenderEffects]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/mask-effect)
