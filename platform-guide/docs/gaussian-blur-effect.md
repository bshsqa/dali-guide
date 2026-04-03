---
id: gaussian-blur-effect
title: "GaussianBlurEffect"
sidebar_label: "GaussianBlurEffect"
---
## Introduction to [GaussianBlurEffect](./gaussian-blur-effect.md)

The `GaussianBlurEffect` is a specialized render-effect module in the DALi framework designed to apply a hardware-accelerated, high-fidelity Gaussian blur to a target view and its children. Unlike basic visual filters, it is engineered for real-time post-processing where performance and visual smoothness are prioritized.

You should use the `GaussianBlurEffect` when your application requires dynamic background blurring (e.g., glassmorphism UI components) or when you need to focus user attention by softening underlying content. It is distinct from other [render-effects](./render-effects.md) because it offers fine-grained control over downscaling, allowing you to optimize GPU throughput by blurring at lower resolutions before upscaling to the native display size.

→ See: [RenderEffects]

## Component Lifecycle and Initialization

The lifecycle of a `GaussianBlurEffect` begins with the factory pattern, ensuring the effect is properly allocated and registered within the DALi engine's render pipeline. Objects should be maintained as handles, allowing the engine to manage the underlying resource cleanup automatically when the handle is released.

### Creating a [GaussianBlurEffect](./gaussian-blur-effect.md)
To instantiate the effect, use the `New` static factory methods. You can initialize it with default parameters or provide a custom starting blur radius for immediate application.

```cpp
#include <dali/ui/gaussian-blur-effect.h>

// Option 1: Create with default radius (10u)
Dali::Ui::GaussianBlurEffect effect = Dali::Ui::GaussianBlurEffect::New();

// Option 2: Create with a specific radius
uint32_t initialRadius = 15;
Dali::Ui::GaussianBlurEffect effectCustom = Dali::Ui::GaussianBlurEffect::New(initialRadius);
```

> Note: The `[GaussianBlurEffect](./gaussian-blur-effect.md)` constructor creates an uninitialized handle. Always check for a null handle before attaching the effect to an actor if the object was default-constructed.

## Configuring Blur Parameters

Tuning the blur parameters is the primary method for balancing visual quality against performance constraints. The effect provides controls to manipulate the blur kernel size and the internal resolution of the blur buffer.

### Radius, Downscaling, and Optimization
The `SetBlurRadius` controls the width of the blur kernel, while `SetBlurDownscaleFactor` controls how much the texture is downsampled before applying the blur. A lower `downscaleFactor` significantly improves performance by reducing the number of pixels processed.

```cpp
// Set blur intensity
effect.SetBlurRadius(20);

// Set downscale factor to 0.5 (blurring at half resolution)
// This is critical for performance on mobile devices.
effect.SetBlurDownscaleFactor(0.5f);

// If only one frame is needed (e.g., static snapshot), set to true.
// Setting to false ensures the effect updates every frame (dynamic).
effect.SetBlurOnce(true);
```

## Animation and Property Transitions

DALi integrates the blur effect directly into the central `Animation` system, allowing for smooth transitions of visual intensity or opacity. This is significantly more efficient than manually updating properties frame-by-frame on the main thread.

### Adding Animations
Use `AddBlurStrengthAnimation` or `AddBlurOpacityAnimation` to define a start and end state for the effect over a specific `TimePeriod`.

```cpp
Dali::Animation blurAnim = Dali::Animation::New(2.0f); // 2 second duration
Dali::TimePeriod period(0.0f, 2.0f);

// Animate blur radius from 5 to 50
effect.AddBlurStrengthAnimation(blurAnim, 
                                Dali::AlphaFunction::EASE_IN_OUT, 
                                period, 
                                5.0f, 
                                50.0f);
blurAnim.Play();
```

## Event Handling and Signals

When `SetBlurOnce(true)` is utilized, the engine performs a single-pass blur render. To know when this resource-intensive operation has successfully completed—for example, to trigger a follow-up animation or hide the original underlying content—you must connect to the `FinishedSignal`.

### The Finished Signal
The `FinishedSignal` provides a callback mechanism to handle the completion of the blur render pass.

```cpp
void OnBlurFinished(Dali::Ui::GaussianBlurEffect& effect) {
    // Perform cleanup or transition logic here
}

// Connecting to the signal
effect.FinishedSignal().Connect(&OnBlurFinished);
```

## Performance Considerations and Threading

The `[GaussianBlurEffect](./gaussian-blur-effect.md)` is a GPU-intensive operation. Because the rendering occurs in the render thread, incorrect usage can lead to frame drops.

*   **Downscale Factors:** Always strive to keep the `downscaleFactor` as low as acceptable for your visual design. A value of 0.25f or 0.5f is often sufficient for background blurring and reduces GPU bandwidth consumption by up to 75%.
*   **BlurOnce Optimization:** If your content is static, always use `SetBlurOnce(true)`. Updating a high-radius blur every single frame is expensive and can impact battery life on low-end hardware.
*   **Thread Safety:** While you can manage `[GaussianBlurEffect](./gaussian-blur-effect.md)` properties from the main thread, the actual GPU buffer processing is asynchronous. Ensure that you do not perform heavy calculations in any callbacks triggered by the `FinishedSignal`, as this will block the [rendering](./rendering.md) path if not handled carefully.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/gaussian-blur-effect)
