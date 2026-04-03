---
id: mask-effect
title: "MaskEffect"
sidebar_label: "MaskEffect"
---
## Understanding the Mask Effect

The `MaskEffect` is a specialized visual component designed to apply stencil or alpha-based masking to UI elements, allowing you to clip the content of a `View` into custom shapes or textures. It is the primary tool for creating non-rectangular UI components, such as circular profile pictures, stylized containers, or dynamic cutouts.

You should use `MaskEffect` when you need to constrain a visual element’s [rendering](./rendering.md) area based on another `View`'s geometry or alpha channel. Unlike general clipping, `MaskEffect` offers granular control over how the mask and source interact, making it distinct for complex UI overlays where performance and shape precision are paramount.

## Creating and Initializing a [MaskEffect](./mask-effect.md)

Initializing a `MaskEffect` requires an existing `Ui::View`, which serves as the stencil or shape provider for the effect. Once instantiated, the effect can be attached to your target component to begin the masking process.

### Instantiating [MaskEffect](./mask-effect.md)
You can create a `MaskEffect` using the factory method `New`. There are two main approaches: one that uses default parameters, and one that provides precise control over the mask’s initial position and scaling.

```cpp
#include <dali-ui/dali-ui.h>

// Assuming 'myView' is an existing View in your application
Dali::Ui::View myView = Dali::Ui::View::New();

// Approach 1: Using default settings
Dali::Ui::MaskEffect effect = Dali::Ui::MaskEffect::New(myView);

// Approach 2: Specifying mode, position, and scale
Dali::Ui::MaskEffect customEffect = Dali::Ui::MaskEffect::New(
    myView, 
    Dali::Ui::MaskEffect::MaskMode::Alpha, // Defined in MaskMode
    Dali::Ui::Vector2(0.0f, 0.0f), 
    Dali::Ui::Vector2(1.0f, 1.0f)
);
```

> Note: The `[MaskEffect](./mask-effect.md)` is a concrete class derived from the `RenderEffect` interface. Adding it to a `View` starts the effect; you must clear the effect manually if you wish to stop it during the view's lifecycle.

## Defining Masking Behavior

The `MaskMode` determines how the system interprets the pixel data of the provided mask source. This configuration allows you to toggle between different rendering strategies, such as alpha-channel masking or luminance-based masking, depending on your source asset's format.

> Note: `MaskMode` constants are platform-level details regarding pixel interpretation; ensure your mask source asset matches the chosen mode (e.g., using an alpha-heavy image for `Alpha` mode).

## Configuring Mask Sources and Targets

To optimize performance, `[MaskEffect](./mask-effect.md)` provides control over whether the mask source and the target view should be re-rendered every frame or captured once. This is critical for static masks that do not change during the application's runtime.

### Managing Rendering Frequency
By default, the engine may re-calculate masks every frame. Using `SetTargetMaskOnce` and `SetSourceMaskOnce` allows you to lock the state, significantly reducing CPU/GPU overhead for static UI elements.

- `SetTargetMaskOnce(bool)`: If set to `true`, the target view is cached after the first render.
- `SetSourceMaskOnce(bool)`: If set to `true`, the mask source is cached after the first render.

```cpp
// Optimize for static UI: cache the target and source to avoid per-frame overhead
effect.SetTargetMaskOnce(true);
effect.SetSourceMaskOnce(true);

bool isTargetStatic = effect.GetTargetMaskOnce();
bool isSourceStatic = effect.GetSourceMaskOnce();
```

## Common Use Cases and Best Practices

`[MaskEffect](./mask-effect.md)` is most commonly used for creating "Avatar" components where a rectangular image must be rendered as a circle, or for complex overlays where an interface requires a specific cutout.

### Implementation Pattern: Circular Avatar
To achieve a circular mask, ensure your `maskView` contains a white circle on a transparent background, and apply the `[MaskEffect](./mask-effect.md)` to the target `View` containing the avatar image.

```cpp
// 1. Create the mask shape
Dali::Ui::View circleMask = Dali::Ui::View::New();
// ... configure circleMask with a circular image ...

// 2. Create the target view
Dali::Ui::View avatar = Dali::Ui::View::New();
// ... load image into avatar ...

// 3. Apply the effect
Dali::Ui::MaskEffect mask = Dali::Ui::MaskEffect::New(circleMask);
avatar.AddEffect(mask);

// 4. If the avatar and mask are static, optimize them
mask.SetTargetMaskOnce(true);
mask.SetSourceMaskOnce(true);
```

> Warning: When using `SetTargetMaskOnce(true)`, any subsequent dynamic changes to the properties of the target `View` (such as changing the image source or modifying child elements) will not be reflected in the mask unless the effect is recreated or updated. If you need dynamic masking (e.g., a mask that moves or scales), keep `targetMaskOnce` set to `false`.

→ See: [RenderEffect] (Parent class documentation)

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/mask-effect)
