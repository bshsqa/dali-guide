---
id: render-effects
title: "render-effects"
sidebar_label: "render-effects"
---
## Introduction to Render Effects

Render effects in DALi provide a powerful framework for applying graphical post-processing and visual modifications to UI actors. By utilizing this framework, developers can easily implement complex visual features like dynamic blurring or arbitrary shape masking without needing to write custom shaders or manage low-level framebuffers.

These effects are designed to be applied to actors to transform their rendered output. Unlike standard transformations that alter properties like position or scale, render effects manipulate the visual representation of the content itself, allowing for sophisticated UI transitions and aesthetic enhancements that remain performant and easy to manage via standard [animation](./animation.md) APIs.

## Applying Effects to Actors

The render effects workflow follows a standard pattern: instantiate the desired effect, configure its properties to suit your design, and apply it to the target actor. Each effect acts as a high-level wrapper that manages the underlying [rendering](./rendering.md) pipeline for you.

> Note: While these effects are highly optimized, applying multiple complex effects to a single actor or nested hierarchy can increase GPU load. Always aim to balance visual complexity with the targeted hardware's performance profile.

## Sub-Components Overview

The `render-effects` family consists of three primary components, each tailored for specific visual tasks.

### Background Blur Effect
`BackgroundBlurEffect` is used to blur the area behind an actor, ideal for frosted glass panels or modal overlays. 
→ See: [[BackgroundBlurEffect](./background-blur-effect.md)]

### Gaussian Blur Effect
`GaussianBlurEffect` applies a standard blur to the target actor and all its children, commonly used for depth-of-field effects or soft UI elements. 
→ See: [[GaussianBlurEffect](./gaussian-blur-effect.md)]

### Mask Effect
`MaskEffect` allows you to clip or mask the appearance of an actor using a source `View`, enabling custom shape cropping or image-based transparencies. 
→ See: [[MaskEffect](./mask-effect.md)]

## Managing Effect Performance

Render effects consume processing resources proportional to the area they cover and the intensity of the effect. To maintain high frame rates, use the following strategies:

*   **Toggle Rendering:** Use the `SetBlurOnce(bool)` method for effects that do not need to [update](./update.md) every frame. If your background is static, setting this to `true` will drastically reduce GPU usage.
*   **Downscaling:** Use `SetBlurDownscaleFactor(float)` to process the effect at a lower resolution than the native screen size. This provides a significant performance boost for blur effects while maintaining an acceptable visual aesthetic.
*   **Animation Control:** Use the `AddBlurStrengthAnimation` and `AddBlurOpacityAnimation` methods to animate properties. These are highly efficient as they are handled within the effect's internal processing loop.

## Common Use Cases and Patterns

A frequent requirement in modern UIs is creating a "frosted glass" look. This is achieved by placing a semi-transparent actor over your content and attaching a `BackgroundBlurEffect` to it.

```cpp
#include <dali/dali.h>
#include <dali/ui/render-effects/background-blur-effect.h>

// Example: Creating a frosted glass effect
void SetupFrostedGlass(Dali::Actor glassPanel)
{
  // Instantiate the effect with a specific radius
  auto blurEffect = Dali::Ui::BackgroundBlurEffect::New(15u);
  
  // Downscale the processing for better performance
  blurEffect.SetBlurDownscaleFactor(0.5f);
  
  // Update the effect once if the content behind is static
  blurEffect.SetBlurOnce(false); 
  
  // The effect is now ready to be applied to the actor
  // Note: Application logic depends on the specific Actor architecture
}
```

### Animating Blur Effects
When a user opens a menu, you may want to animate the blur intensity to create a smooth transition.

```cpp
// Assuming 'myBlurEffect' is an existing BackgroundBlurEffect
// 'myAnimation' is an existing Dali::Animation object

Dali::TimePeriod period(0.0f, 0.5f); // 0.5 second animation
myBlurEffect.AddBlurStrengthAnimation(
  myAnimation, 
  Dali::AlphaFunction::EASE_IN_OUT, 
  period, 
  0.0f, 
  20.0f
);
myAnimation.Play();
```

> Warning: When using `SetBlurOnce(true)`, the `FinishedSignal()` is the only reliable way to know when the [rendering](./rendering.md) pass has completed. Avoid performing operations that depend on the blurred content before this signal is emitted.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/render-effects)
