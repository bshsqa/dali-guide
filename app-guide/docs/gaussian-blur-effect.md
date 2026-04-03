---
id: gaussian-blur-effect
title: "GaussianBlurEffect"
sidebar_label: "GaussianBlurEffect"
---
## Introduction to Gaussian Blur Effect

The `GaussianBlurEffect` is a specialized render-effect that applies a smooth, mathematical blur to a target actor and its entire child hierarchy. Unlike simple filter effects, this module provides high-performance spatial blurring by leveraging the underlying graphics pipeline to process the view contents.

You should use `GaussianBlurEffect` when your application requires polished UI elements such as frosted-glass overlays, depth-of-field effects, or background blurring for modal popups. It is distinct from other [render-effects](./render-effects.md) because it offers granular control over sampling quality through downscaling and radius configuration, allowing you to strike a balance between visual fidelity and GPU overhead. 
→ See: [RenderEffects]

## Creating and Applying the Effect

To begin blurring an element, you must instantiate the effect and associate it with your UI target. The constructor provides both a default initialization and an option to specify an initial radius for immediate visual impact.

### Instantiating the Effect
The `New()` factory methods create an instance of the effect. Use the variant with a `blurRadius` parameter to set the visual intensity immediately upon creation.

```cpp
using namespace Dali::Ui;

// Create an effect with the default radius (10)
GaussianBlurEffect effect = GaussianBlurEffect::New();

// Create an effect with a specific radius (e.g., 20)
GaussianBlurEffect customEffect = GaussianBlurEffect::New(20u);
```

> Note: `[GaussianBlurEffect](./gaussian-blur-effect.md)` is a handle-based class; ensure the object remains in scope as long as the effect is required on the UI component.

## Configuring Blur Quality and Intensity

Fine-tuning the blur is essential for achieving the desired aesthetic while maintaining smooth frame rates. The blur quality is primarily dictated by the interaction between the blur radius and the downscale factor.

### Adjusting Radius and Downscaling
The blur radius determines the spread of the blur, while the downscale factor reduces the texture resolution used for the blurring pass to save memory and processing cycles.

*   **SetBlurRadius**: Defines the pixel-range of the blur. Higher values increase the blur spread but may incur a higher performance cost.
*   **SetBlurDownscaleFactor**: Accepts a float between `0.0f` and `1.0f`. A lower value (e.g., `0.2f`) downscales the texture significantly, which is the most effective way to optimize performance on high-resolution screens.

```cpp
void ConfigureEffect(GaussianBlurEffect& effect)
{
    // High radius for a strong, soft look
    effect.SetBlurRadius(15u);
    
    // Reduce resolution to 25% of the original for better performance
    effect.SetBlurDownscaleFactor(0.25f);
}
```

## Animating Blur Properties

Dynamic transitions can be created by animating the blur's intensity or its visibility. The framework provides dedicated methods to integrate these properties into your existing `Animation` objects.

### Adding Animations
These methods allow you to interpolate the blur strength or opacity over a specified `TimePeriod` using a defined `AlphaFunction`. 

*   **AddBlurStrengthAnimation**: Animates the intensity of the blur.
*   **AddBlurOpacityAnimation**: Animates the transparency of the blurred effect layer.

```cpp
void SetupBlurAnimation(GaussianBlurEffect& effect, Animation& myAnimation)
{
    TimePeriod period(0.0f, 1.0f); // 1 second duration
    
    // Transition blur strength from 0 to 20
    effect.AddBlurStrengthAnimation(myAnimation, AlphaFunction::EASE_IN_OUT, period, 0.0f, 20.0f);
    
    // Fade the effect in
    effect.AddBlurOpacityAnimation(myAnimation, AlphaFunction::LINEAR, period, 0.0f, 1.0f);
    
    myAnimation.Play();
}
```

## Handling Completion Signals

When performing one-time blur operations, it is often necessary to know when the rendering of the blurred frame is finished. The `FinishedSignal` provides a callback mechanism to trigger logic precisely when the GPU completes the operation.

### Using FinishedSignal
This signal is specifically useful when `BlurOnce` is set to `true`, as it notifies your application that the snapshot has been successfully processed.

```cpp
void OnBlurFinished(GaussianBlurEffect& effect)
{
    // Logic to execute once the blur has been rendered
}

// Inside your initialization code:
effect.SetBlurOnce(true);
effect.FinishedSignal().Connect(&OnBlurFinished);
```

## Performance Optimization Tips

Efficient use of `[GaussianBlurEffect](./gaussian-blur-effect.md)` prevents UI stuttering. In scenarios where the blurred content is static, updating the blur texture every frame is unnecessary and resource-intensive.

### Utilizing BlurOnce
The `SetBlurOnce(bool)` method is a critical optimization tool. When set to `true`, the framework renders the blur effect exactly once and caches the result. This is ideal for static background elements or captured UI states where the underlying content does not change.

*   `SetBlurOnce(false)` (Default): The effect refreshes every frame, suitable for dynamic, animated content beneath the blur.
*   `SetBlurOnce(true)`: The effect renders only once, significantly reducing GPU load for static scenes.

> Warning: Always prefer `BlurOnce = true` for static backgrounds. If you must animate elements behind a blurred view, ensure your `downscaleFactor` is set to a low value (e.g., `0.3f` or lower) to maintain the target frame rate.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/gaussian-blur-effect)
