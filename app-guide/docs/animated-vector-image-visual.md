---
id: animated-vector-image-visual
title: "AnimatedVectorImageVisual"
sidebar_label: "AnimatedVectorImageVisual"
---
## Introduction to Animated Vector Image Visuals

The `AnimatedVectorImageVisual` is a specialized visual type within the DALi framework designed to render and animate scalable vector graphics, such as Lottie files. Unlike standard `ImageVisual` or `VectorImageVisual`, which are intended for static or simple assets, this visual provides a robust framework for handling complex, multi-frame vector animations with granular control over playback and state.

Use the `AnimatedVectorImageVisual` whenever your application requires high-fidelity, resolution-independent motion graphics that maintain crisp quality at any scale. It is distinct because it encapsulates the [animation](./animation.md) engine logic directly within the visual, allowing for optimized memory management and performance-aware [rendering](./rendering.md) of vectorized motion assets.

## Creating and Configuring the Visual

To create an animated vector visual, you define a `Property::Map` that specifies the asset location and desired playback configuration, then pass this map to the `VisualFactory`. This declarative approach ensures that the visual is set up correctly with all required initial parameters before being applied to an actor.

### Defining the Property Map
You must provide the `Visual::Property::TYPE` and the file URL. You can also define looping behavior, play state, and playback range at creation time.

```cpp
#include <dali/public-api/dali-adaptor-common.h>
#include <dali/public-api/visuals/visual-factory-common.h>

// Create a visual property map
Dali::Property::Map propertyMap;
propertyMap.Insert(Dali::Toolkit::Visual::Property::TYPE, Dali::Toolkit::Visual::ANIMATED_VECTOR_IMAGE);
propertyMap.Insert(Dali::Toolkit::Visual::Property::URL, "animation.json");
propertyMap.Insert(Dali::Toolkit::AnimatedVectorImageVisual::Property::LOOP_COUNT, 1);
propertyMap.Insert(Dali::Toolkit::AnimatedVectorImageVisual::Property::PLAY_STATE, Dali::Toolkit::AnimatedVectorImageVisual::PlayState::PLAYING);

// Use the factory to create the visual
Dali::Toolkit::Visual::Base visual = Dali::Toolkit::VisualFactory::Get().CreateVisual(propertyMap);
```

> Note: Ensure the provided URL points to a supported vector animation format (typically JSON-based Lottie files) accessible by the application's file system permissions.

## Managing Animation Playback

Managing the lifecycle of your animation involves modifying the `PLAY_STATE` property of the visual. This allows you to pause, stop, or resume playback based on user interaction or application events without re-instantiating the visual.

### Changing Playback State
To change the state, use the `SetProperty` method on the visual or the actor holding the visual. The `PLAY_STATE` property accepts an enumeration that dictates the immediate transition of the animation engine.

```cpp
// Assuming 'myVisual' is an existing AnimatedVectorImageVisual
myVisual.SetProperty(Dali::Toolkit::AnimatedVectorImageVisual::Property::PLAY_STATE, 
                     Dali::Toolkit::AnimatedVectorImageVisual::PlayState::PAUSED);
```

The available states are:
- `PLAYING`: Starts or resumes the animation.
- `PAUSED`: Freezes the animation at the current frame.
- `STOPPED`: Returns the animation to the initial state (typically the first frame).

## Controlling Playback Progress

For dynamic interfaces, you may want to scrub through an animation based on a slider value or control the speed of the playback for stylistic effects. These properties allow for fine-grained control over the temporal progression of the visual.

### Scrubbing and Speed
The `CURRENT_FRAME` property allows you to jump to specific points in the animation, while `SPEED_FACTOR` determines the playback rate relative to the original duration.

```cpp
// Jump to the middle of the animation
myVisual.SetProperty(Dali::Toolkit::AnimatedVectorImageVisual::Property::CURRENT_FRAME, 50.0f);

// Play the animation at half speed
myVisual.SetProperty(Dali::Toolkit::AnimatedVectorImageVisual::Property::SPEED_FACTOR, 0.5f);
```

> Warning: When setting `CURRENT_FRAME`, values exceeding the total frame count of the animation will be clamped to the duration defined in the asset.

## Responding to Animation Events

To synchronize app logic with the visual, you can connect to signals provided by the visual's controller. These signals notify your application when the animation reaches significant milestones, such as completion or a loop iteration.

### Connecting to Signals
Signals are typically accessed via the object returned by the visual controller.

```cpp
// Define a callback function
void OnAnimationFinished(Dali::Toolkit::AnimatedVectorImageVisual::Controller controller)
{
  // Logic to execute when animation completes
}

// Connect the signal
myVisual.FinishedSignal().Connect(&OnAnimationFinished);
```

## Performance and Resource Optimization

Because animated vectors involve real-time rasterization of vector paths, large or complex files can impact CPU and GPU usage. Following these best practices ensures your animations remain fluid and do not cause dropped frames.

- **Limit Complexity:** Avoid excessively complex vector paths within your source files; simpler paths lead to faster rasterization.
- **Reuse Visuals:** If you need to display the same animation multiple times, reuse the same `Property::Map` or visual instance where possible to minimize parsing overhead.
- **State Management:** Always set the `PLAY_STATE` to `STOPPED` or `PAUSED` when the visual is not visible or off-screen to save battery and [rendering](./rendering.md) resources.

> Note: Advanced optimizations related to memory buffers and multi-threaded rasterization are managed by the internal DALi [rendering](./rendering.md) pipeline. If you encounter specific performance bottlenecks, refer to the platform-level detail guides on asset optimization.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animated-vector-image-visual)
