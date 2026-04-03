---
id: background-blur-effect
title: "BackgroundBlurEffect"
sidebar_label: "BackgroundBlurEffect"
---
## Introducing Background Blur Effect

The `BackgroundBlurEffect` is a specialized render effect designed to dynamically blur the background content located behind a specific UI component. It is the ideal solution for implementing "frosted-glass" UI designs, modal overlays, and depth-aware interfaces where you need to soften underlying content to maintain focus on foreground elements.

Unlike standard static image filters, `BackgroundBlurEffect` is highly performant and specifically architected to integrate within the DALi [rendering](./rendering.md) pipeline, providing both real-time blurring for active animations and a static `BlurOnce` mode for optimized performance.

## Creating and Applying the Effect

To use the `BackgroundBlurEffect`, you instantiate it via the Factory pattern and apply it to your desired UI element. This effect calculates the visual contribution of objects behind the host actor and applies a Gaussian-style blur to that specific area.

### Instantiating the Effect
The `New()` methods serve as the primary entry points for [object](./object.md) creation. You can either use the default constructor, which sets an initial blur radius of 10, or provide a specific radius to suit your design requirements.

```cpp
#include <dali/dali.h>
#include <dali/devel-api/ui/background-blur-effect.h>

using namespace Dali::Ui;

// Create a default effect
BackgroundBlurEffect blurEffect = BackgroundBlurEffect::New();

// Create an effect with a custom radius of 20
BackgroundBlurEffect customBlur = BackgroundBlurEffect::New(20u);
```

## Configuring Blur Quality and Intensity

Fine-tuning the blur is essential for balancing visual aesthetics with GPU overhead. You can control how blurred the background appears through the blur radius and the downscale factor, which reduces the texture resolution used during the blur pass.

### Adjusting Blur Parameters
The `SetBlurRadius` method defines the spread of the blur, while `SetBlurDownscaleFactor` controls the scale of the off-screen buffer used for the calculation.

> **Note:** A `downscaleFactor` closer to 1.0 provides higher visual fidelity but increases GPU load, whereas lower values (e.g., 0.1f) significantly improve performance at the cost of potential aliasing.

```cpp
// Set a higher blur radius for a more intense frosted effect
blurEffect.SetBlurRadius(30u);

// Lower the downscale factor to 0.2f to improve rendering performance
blurEffect.SetBlurDownscaleFactor(0.2f);
```

### Static vs. Dynamic Blurring
By default, the effect renders every frame. If your background content is static, you can toggle `SetBlurOnce(true)` to capture the background image once and cache it, drastically reducing the frame-by-frame rendering cost.

```cpp
// Optimize performance for static background content
blurEffect.SetBlurOnce(true);

bool isStatic = blurEffect.GetBlurOnce(); // Returns true
```

## Managing Source and Stopper Actors

By default, the `[BackgroundBlurEffect](./background-blur-effect.md)` samples all content behind the host actor. You can refine this by using source and stopper actors to create complex occlusion masks or to limit the depth of the blur effect within the scene hierarchy.

### Defining Influence
`SetSourceActor` restricts the blur input to a specific sub-tree, while `SetStopperActor` acts as a limit for the rendering pass, preventing the effect from capturing content behind the specified actor.

```cpp
// Limit the blur to only include a specific container
blurEffect.SetSourceActor(myContentContainer);

// Stop the capture process when hitting a background panel
blurEffect.SetStopperActor(backgroundPanel);
```

## Animating Blur Transitions

The `[BackgroundBlurEffect](./background-blur-effect.md)` provides built-in methods to integrate directly with DALi `Animation` objects. This allows you to smoothly transition the intensity or the opacity of the blur effect as part of a larger UI sequence, such as a menu opening or a modal appearing.

### Adding Blur Animations
You can animate the blur strength or the opacity of the resulting blur texture. These methods require an `Animation` object, an `AlphaFunction` for timing, and a `TimePeriod` to define the duration and start delay.

```cpp
Dali::Animation animation = Dali::Animation::New(1.0f); // 1-second animation

// Smoothly transition from zero blur to high blur
blurEffect.AddBlurStrengthAnimation(
    animation, 
    Dali::AlphaFunction::EASE_IN_OUT, 
    Dali::TimePeriod(0.0f, 1.0f), 
    0.0f, 
    20.0f
);

animation.Play();
```

## Handling Blur Completion Signals

When working in `BlurOnce` mode, it is often necessary to know exactly when the rendering operation has completed to synchronize further UI updates or transitions.

### Using the FinishedSignal
The `FinishedSignal()` provides a callback mechanism that notifies the application once the target actor has been rendered and the blur texture has been successfully cached.

```cpp
// Connect to the signal to handle completion
blurEffect.FinishedSignal().Connect([](BackgroundBlurEffect& effect) {
    // Perform cleanup or update UI state here
});
```

> **Warning:** This signal is intended for use primarily when `SetBlurOnce(true)` is active. If the effect is [rendering](./rendering.md) every frame, this signal will not behave as a "completion" notifier in the traditional sense.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/background-blur-effect)
