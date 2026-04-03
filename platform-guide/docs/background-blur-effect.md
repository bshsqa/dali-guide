---
id: background-blur-effect
title: "BackgroundBlurEffect"
sidebar_label: "BackgroundBlurEffect"
---
## Introduction to [BackgroundBlurEffect](./background-blur-effect.md)

`BackgroundBlurEffect` is a specialized [rendering](./rendering.md) component designed to apply real-time gaussian-style blur filters to the content behind a specific UI element. It is distinct from standard static image filters because it dynamically samples and blurs the underlying scene graph hierarchy in real-time, making it ideal for glass-morphism UIs, modal dialog backgrounds, and dynamic overlays.

Use `BackgroundBlurEffect` when you need to transform a clear, behind-the-view scene segment into a blurred background that adjusts as the scene hierarchy changes. Unlike general `RenderEffect` variants, this component is highly optimized for background-caching and provides granular control over the sampling downscale factor to balance visual softness with GPU performance.

## Effect Configuration and Parameters

To achieve a desired aesthetic, `BackgroundBlurEffect` provides controls to manipulate the intensity of the blur and the quality of the sample texture. These parameters directly influence both the GPU fragment shader complexity and the visual fidelity of the blur effect.

### Blur Radius and Downscale
The `BlurRadius` defines the spread of the gaussian filter, while the `DownscaleFactor` determines the resolution of the buffer used to calculate the blur.

* **SetBlurRadius(uint32_t blurRadius)**: Sets the pixel spread for the blur kernel. A higher radius creates a softer, more distributed blur but increases computational cost.
* **SetBlurDownscaleFactor(float downscaleFactor)**: Sets the downscale multiplier (0.0f to 1.0f). Lower values dramatically reduce the pixel count processed by the blur pass, offering a high-performance path for large background areas.

```cpp
// Example: Configuring a high-performance blur
auto blurEffect = Dali::Ui::BackgroundBlurEffect::New(15u);
blurEffect.SetBlurDownscaleFactor(0.25f); // Render blur at 1/4 resolution for efficiency
```

> Note: Using a low `BlurDownscaleFactor` can introduce aliasing artifacts; ensure that the `BlurRadius` is balanced against the downscale factor to maintain visual smoothness.

## Source and Stopper Actor Management

`[BackgroundBlurEffect](./background-blur-effect.md)` allows for precise control over which parts of the scene graph are included in the blur computation. By default, the effect captures everything behind the owner view, but you can restrict this scope to target specific sub-trees or exclude certain layers.

### Managing Scope
* **SetSourceActor(Dali::Actor sourceActor)**: Specifies the specific actor sub-tree to be used as the source for the blur input. If left as an empty handle, the system defaults to the immediate background content.
* **SetStopperActor(Dali::Actor stopperActor)**: Defines a hierarchy "ceiling." The blur rendering will traverse the tree and stop when it encounters this actor, ensuring that upper-level UI overlays are excluded from the blur source.

```cpp
// Example: Scoping the blur to a specific container
Dali::Actor mainContent = GetMainSceneRoot();
Dali::Actor modalContainer = GetModalView();

blurEffect.SetSourceActor(mainContent);
blurEffect.SetStopperActor(modalContainer);
```

## Animation and Interpolation APIs

Dynamic UI transitions often require the blur effect to ramp up or fade out in sync with other visual elements. The engine provides specialized animation methods that allow the blur intensity and visibility to be driven by the `Animation` system, ensuring perfectly synchronized state changes.

### Animating Blur Properties
* **AddBlurStrengthAnimation**: Interpolates the blur radius over time.
* **AddBlurOpacityAnimation**: Interpolates the transparency of the blur layer itself.

```cpp
// Example: Animating a background blur transition
Dali::Animation blurAnimation = Dali::Animation::New(1.0f);
blurEffect.AddBlurStrengthAnimation(blurAnimation, Dali::AlphaFunction::EASE_IN, Dali::TimePeriod(0.0f, 1.0f), 0.0f, 20.0f);
blurAnimation.Play();
```

## Event Handling and Signal Lifecycle

When using `SetBlurOnce(true)`, the system renders the blur result as a static snapshot. Because this is an asynchronous process, the `FinishedSignal` is crucial for determining when the rendering task has completed.

### Signal Usage
* **FinishedSignal()**: Connects a callback that triggers once the target background has been successfully captured and blurred. This is primarily useful for transitioning states after a single-shot blur has been finalized.

```cpp
// Example: Handling completion of a one-time blur
blurEffect.SetBlurOnce(true);
blurEffect.FinishedSignal().Connect([](Dali::Ui::BackgroundBlurEffect& effect) {
    // Perform follow-up logic, such as enabling interaction
});
```

## Performance Optimization Guidelines

The `SetBlurOnce` API is the most critical tool for performance management. By default, the system renders the blur every frame, which provides the most accurate results for moving backgrounds but imposes a constant GPU load.

* **SetBlurOnce(bool)**: Set to `true` to capture a static background snapshot. This is highly recommended for static backgrounds or windows where the content does not update frequently.
* **SetBlurOnce(false)**: Use only when the content behind the blur is animated or interactive.

> Warning: Enabling continuous updates (`SetBlurOnce(false)`) while using a high `BlurRadius` will significantly increase fragment shader load. Always prefer `SetBlurOnce(true)` for non-animated backgrounds.

## Integration and Thread Safety

The `[BackgroundBlurEffect](./background-blur-effect.md)` operates within the DALi render pipeline. While the API handles the majority of the synchronization, developers must ensure that modifications to actor hierarchy—specifically calling `SetSourceActor` or `SetStopperActor`—occur on the UI thread to prevent race conditions within the scene graph update cycle.

* **Thread Safety**: All `Set` and `Get` methods are designed to be called from the UI thread. Cross-thread interaction with these properties is not supported and will lead to undefined behavior in the [rendering](./rendering.md) process.
* **Lifecycle**: The effect lifecycle is bound to the actor it is attached to. Ensure that if you remove the associated actor from the stage, you manage the lifecycle of the effect handle correctly to prevent memory leaks in the render backend.

→ See: [RenderEffects]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/background-blur-effect)
