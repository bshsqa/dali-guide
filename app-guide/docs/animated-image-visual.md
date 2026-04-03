---
id: animated-image-visual
title: "AnimatedImageVisual"
sidebar_label: "AnimatedImageVisual"
---
## Introduction to Animated Image Visual

The `AnimatedImageVisual` is a specialized DALi visual designed to render multi-frame image formats, such as animated GIFs, within a UI component. Unlike standard image [visuals](./visuals.md) that display static content, this visual manages the temporal sequencing of frames, providing developers with granular control over playback state, looping behavior, and frame navigation.

Use `AnimatedImageVisual` whenever your application requires dynamic, frame-based content that is packaged as a single animated file. If you need to display standard, non-animated [images](./images.md), use the `ImageVisual` → See: [ImageVisual]. If you require complex, multi-layered animations involving vector graphics or keyframe interpolation, consider `LottieAnimation` or `AnimatedVectorImageVisual` → See: [[AnimatedVectorImageVisual](./animated-vector-image-visual.md)].

## Configuring Visual Properties

To initialize an `AnimatedImageVisual`, you provide a `Property::Map` to the visual factory that defines the source and the playback parameters. This map acts as the configuration descriptor for the visual's lifecycle.

The following keys are primarily used to define the visual:

*   **Visual::Property::TYPE**: Must be set to `Visual::ANIMATED_IMAGE`.
*   **ImageVisual::Property::URL**: A string representing the file path or URI of the animated image.
*   **[AnimatedImageVisual](./animated-image-visual.md)::Property::BATCH_SIZE**: An integer defining how many frames to cache in memory to improve smoothness.
*   **[AnimatedImageVisual](./animated-image-visual.md)::Property::CACHE_SIZE**: The total number of frames to keep in the cache.
*   **[AnimatedImageVisual](./animated-image-visual.md)::Property::FRAME_DELAY**: An integer specifying the base delay between frames in milliseconds (if the file metadata does not provide it).
*   **[AnimatedImageVisual](./animated-image-visual.md)::Property::LOOP_COUNT**: An integer representing the number of times to loop the [animation](./animation.md); 0 indicates infinite looping.

```cpp
// Example: Creating an AnimatedImageVisual property map
Property::Map visualMap;
visualMap[Visual::Property::TYPE] = Visual::ANIMATED_IMAGE;
visualMap[ImageVisual::Property::URL] = "example_animation.gif";
visualMap[AnimatedImageVisual::Property::LOOP_COUNT] = 1; // Play once
visualMap[AnimatedImageVisual::Property::BATCH_SIZE] = 5;

// Apply to a Control
auto control = Control::New();
control.SetProperty(Control::Property::BACKGROUND, visualMap);
```

## Controlling Playback Behavior

The `[AnimatedImageVisual](./animated-image-visual.md)` exposes property-based control to manipulate the animation state during runtime. By modifying the `[AnimatedImageVisual](./animated-image-visual.md)::Property::PLAY_STATE` property, you can start, pause, or stop the playback sequence.

*   **PLAY_STATE**: Accepts `Toolkit::[AnimatedImageVisual](./animated-image-visual.md)::PlayState::PLAYING`, `PAUSED`, or `STOPPED`. 
*   **CURRENT_FRAME**: A read-write property allowing you to jump to a specific index in the animation sequence.

> Note: Changing the `CURRENT_FRAME` while the animation is `PLAYING` will cause the animation to resume from the newly set index.

```cpp
// Controlling playback
// Pause the animation
control.SetProperty(AnimatedImageVisual::Property::PLAY_STATE, AnimatedImageVisual::PlayState::PAUSED);

// Jump to the 10th frame
control.SetProperty(AnimatedImageVisual::Property::CURRENT_FRAME, 10);

// Resume playback
control.SetProperty(AnimatedImageVisual::Property::PLAY_STATE, AnimatedImageVisual::PlayState::PLAYING);
```

## Handling Playback Signals

The `[AnimatedImageVisual](./animated-image-visual.md)` provides signals that allow your application to react to the lifecycle of the animation, such as reaching the end of the loop or encountering playback issues. Accessing these signals is typically done via the `Visual` handle within the control.

*   **FrameReachedSignal()**: Emitted when a specific frame index is reached.
*   **FinishedSignal()**: Emitted when the animation sequence completes its loop count.

```cpp
// Connecting to the finished signal
auto visual = control.GetVisual(Control::Property::BACKGROUND);
auto animatedVisual = visual.DownCast<AnimatedImageVisual>();

if (animatedVisual)
{
  animatedVisual.FinishedSignal().Connect([](AnimatedImageVisual visual) {
    // Handle completion logic here
  });
}
```

## Optimizing Memory and Performance

Animated images can be memory-intensive because they require keeping multiple bitmap buffers in memory simultaneously. Proper configuration of cache parameters is essential for performance on resource-constrained devices.

*   **BATCH_SIZE**: High batch sizes provide smoother playback but increase peak memory usage. Match this value to the device's available memory.
*   **CACHE_SIZE**: Setting this to a lower value reduces the memory footprint at the cost of potential frame stuttering if the underlying decoder cannot keep up with the render loop.

> Warning: Always prefer formats optimized for hardware decoding. Extremely large or high-frame-rate GIFs can cause significant CPU/GPU overhead; if performance is a concern, consider converting the sequence into an optimized sprite sheet or a video asset.

## Common Implementation Patterns

The standard pattern for using an `[AnimatedImageVisual](./animated-image-visual.md)` involves creating a `Control`, populating its background with the visual property map, and adding it to the `Stage`.

```cpp
// Basic setup pattern
void CreateAnimation(Actor parent)
{
  Property::Map visualMap;
  visualMap[Visual::Property::TYPE] = Visual::ANIMATED_IMAGE;
  visualMap[ImageVisual::Property::URL] = "sequence.gif";
  
  Control imageControl = Control::New();
  imageControl.SetProperty(Actor::Property::SIZE, Vector2(200.0f, 200.0f));
  imageControl.SetProperty(Control::Property::BACKGROUND, visualMap);
  
  parent.Add(imageControl);
}
```

This pattern ensures that the visual is correctly bound to the actor's size and lifecycle, allowing DALi to manage the GPU resources automatically when the actor is removed from the stage.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animated-image-visual)
