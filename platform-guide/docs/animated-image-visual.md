---
id: animated-image-visual
title: "AnimatedImageVisual"
sidebar_label: "AnimatedImageVisual"
---
## Introduction to Animated Image Visual

The `AnimatedImageVisual` is a specialized component within the DALi visual system designed to render and playback frame-sequenced image formats, such as animated GIFs and WebP files. Unlike standard `ImageVisual` types which handle static assets, this visual manages frame buffers, playback timing, and loop logic to provide efficient, hardware-accelerated animations.

Use the `AnimatedImageVisual` when your UI requires high-performance, frame-based content that needs to be treated as a unified visual [object](./object.md) within a `Control` or `Actor`. It is distinct from `AnimatedVectorImageVisual` (→ See: [[AnimatedVectorImageVisual](./animated-vector-image-visual.md)]) as it focuses specifically on raster-based multi-frame formats rather than path-based vector data.

## Visual Property Schema

Configuration of an `AnimatedImageVisual` is performed via a `Property::Map` passed during visual creation or via the `Visual::Base::SetProperties` interface. These properties define the playback constraints of the [animation](./animation.md) container.

### Core Configuration Keys

*   **`Toolkit::Visual::Property::TYPE`**: Set to `Toolkit::Visual::ANIMATED_IMAGE`.
*   **`Toolkit::ImageVisual::Property::URL`**: A `std::string` specifying the file path or URI of the animated image resource.
*   **`Toolkit::AnimatedImageVisual::Property::LOOP_COUNT`**: An `int` specifying how many times the [animation](./animation.md) should repeat. Setting this to `-1` creates an infinite loop.
*   **`Toolkit::AnimatedImageVisual::Property::BATCH_SIZE`**: An `int` defining the number of frames to decode in advance, useful for tuning the trade-off between memory usage and frame-drop prevention.

```cpp
// Example: Creating an animated image visual
Property::Map propertyMap;
propertyMap.Insert(Toolkit::Visual::Property::TYPE, Toolkit::Visual::ANIMATED_IMAGE);
propertyMap.Insert(Toolkit::ImageVisual::Property::URL, "animation.gif");
propertyMap.Insert(Toolkit::AnimatedImageVisual::Property::LOOP_COUNT, -1);

// Apply to a control
mControl.SetProperty(Toolkit::Control::Property::BACKGROUND, propertyMap);
```

## Playback Control and State Management

Controlling the lifecycle of the animation is handled through the `Visual::Base::DoAction` interface using the `[AnimatedImageVisual](./animated-image-visual.md)::Action` namespace. This allows for runtime state transitions without re-creating the visual object.

### Action Types
The `Action::Type` enum provides the entry points for manipulating the animation stream:
*   **`PLAY`**: Resumes or starts the animation from the current frame.
*   **`PAUSE`**: Freezes the animation at the current frame index.
*   **`STOP`**: Terminates playback and resets the internal frame pointer to the beginning.

```cpp
// Example: Controlling playback dynamically
// Assuming 'myVisual' is a valid Handle to an AnimatedImageVisual
myVisual.DoAction(Toolkit::AnimatedImageVisual::Action::PAUSE, Property::Null);

// ... later ...
myVisual.DoAction(Toolkit::AnimatedImageVisual::Action::PLAY, Property::Null);
```

> Warning: Calling `PLAY` on a visual that is already playing has no effect, but calling `STOP` will reset the state, meaning a subsequent `PLAY` will restart the animation from the first frame.

## Resource Management and Lifecycle

`[AnimatedImageVisual](./animated-image-visual.md)` employs a sophisticated internal caching mechanism to ensure smooth playback while maintaining a strict memory footprint. Since animated images can consume significant memory if expanded into full bitmaps, the visual uses a streaming buffer approach.

*   **Buffer Management**: Frames are decoded on demand. The `BATCH_SIZE` property dictates the pre-fetch window; increasing this prevents stuttering on high-frequency animations at the cost of heap memory.
*   **Lifecycle Binding**: The visual lifecycle is bound to the `Actor` container. When the host actor is removed from the stage or hidden, the visual internally pauses the decoder threads to preserve battery and CPU cycles.

## Integration with DevelAnimatedImageVisual

The `DevelAnimatedImageVisual` namespace provides advanced, low-level access to the internal state of the visual, which is required for complex UI components that need to respond to specific frame transitions or precise metadata.

### Advanced Signal Handling
The `DevelAnimatedImageVisual::Signal::Type` allows developers to register callbacks for state-change events, such as when an animation completes its final loop or encounters an error during decoding.

```cpp
// Example: Registering for frame completion signals
void OnAnimationFinished(AnimatedImageVisual visual)
{
  // Logic to handle completion
}

// Using Devel API to connect signals
Dali::Toolkit::DevelAnimatedImageVisual::FinishedSignal(myVisual).Connect(this, &MyClass::OnAnimationFinished);
```

> Note: Accessing `Devel` APIs implies a dependency on internal headers. Ensure your project configuration includes the appropriate `devel-api` search paths.

## Performance Optimization Guidelines

To ensure smooth animations within complex scenes, observe the following optimization practices:

1.  **Frame Size Constraints**: Always attempt to source animated assets that match the intended display size in the UI. Scaling large high-resolution GIFs at runtime incurs a heavy cost on the GPU for every frame transition.
2.  **Memory Throttling**: If your application uses multiple animated images simultaneously, use smaller `BATCH_SIZE` values to avoid exceeding the device's memory limits.
3.  **Texture Atlas**: Where possible, utilize pre-exported sprite sheets and `ImageVisual` sequences rather than format-based animated images if the animation contains a high number of frames, as this allows for native texture atlas batching.
4.  **Avoid Redundant Initialization**: Reuse the `Property::Map` for [common](./common.md) [visuals](./visuals.md) to reduce the overhead of string lookups and resource allocation during layout updates.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animated-image-visual)
