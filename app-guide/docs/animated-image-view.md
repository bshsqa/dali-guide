---
id: animated-image-view
title: "AnimatedImageView"
sidebar_label: "AnimatedImageView"
---
## Introduction to [AnimatedImageView](./animated-image-view.md)

The `AnimatedImageView` is a specialized `View` component designed specifically for [rendering](./rendering.md) multi-frame image formats, such as animated GIFs or WebP files. While standard image views are optimized for static textures, `AnimatedImageView` manages the decoding and frame-sequencing logic required to display motion within your UI.

Use this component whenever you need to integrate animated sequences into your application, such as loading spinners, expressive stickers, or decorative UI elements. It is distinct from standard view containers because it provides dedicated controls for [animation](./animation.md) playback, including play, pause, and stop commands.

→ See: [[ImageView](./static-image-view.md)]

## Getting Started with [AnimatedImageView](./animated-image-view.md)

To use an `AnimatedImageView`, you must instantiate it using the static `New()` factory method. This method initializes the internal handle and prepares the component for [rendering](./rendering.md), optionally accepting a resource URL string to begin loading immediately.

Once created, you can add it to your UI hierarchy like any other `Dali::Ui::View`. 

```cpp
#include <Dali/Ui/AnimatedImageView.h>
#include <Dali/Ui/View.h>

void CreateMyAnimatedView(Dali::Ui::View parent)
{
  // Instantiate the animated view with a source URL
  auto animatedView = Dali::Ui::AnimatedImageView::New("example_animation.gif");

  // Add the animated view to the existing UI hierarchy
  parent.Add(animatedView);
}
```

## Configuring Image Resources and Styling

`[AnimatedImageView](./animated-image-view.md)` allows you to define the visual source of your animation and apply stylistic transformations. You can update the resource source dynamically at runtime and apply a color tint to the image using the provided color API.

### Managing Source URLs
The `SetResourceUrl` method allows you to change the image source after initialization. This is useful for swapping animations based on user interaction or application state.

```cpp
void UpdateAnimation(Dali::Ui::AnimatedImageView& view)
{
  // Change the resource URL dynamically
  view.SetResourceUrl("new_animation.webp");
  
  // Verify the URL
  Dali::String currentUrl = view.GetResourceUrl();
}
```

### Applying Image Color
The `SetImageColor` method applies a `UiColor` multiplier to the image, which acts as a tint or transparency filter over the existing pixel data.

```cpp
void TintAnimation(Dali::Ui::AnimatedImageView& view)
{
  // Apply a semi-transparent blue tint
  view.SetImageColor(Dali::Ui::UiColor(0.0f, 0.0f, 1.0f, 0.5f));
}
```

## Controlling Animation Playback

`[AnimatedImageView](./animated-image-view.md)` provides fine-grained control over the animation lifecycle. You can trigger or halt movement and define how many times the sequence repeats before finishing.

### Playback Commands
- `Play()`: Starts or resumes the animation from its current frame.
- `Pause()`: Freezes the animation at the current frame.
- `Stop()`: Halts the animation and resets the internal state to the first frame.

### Looping Behavior
Use `SetLoopCount` to define the total number of playback iterations. A value of 0 typically indicates an infinite loop in most implementations.

```cpp
void SetupPlayback(Dali::Ui::AnimatedImageView& view)
{
  // Set the animation to loop 3 times
  view.SetLoopCount(3);
  
  // Start the animation
  view.Play();
}
```

## Monitoring Loading and Playback Status

Because image loading is an asynchronous operation, you may need to wait for the resource to be fully prepared before performing actions, such as starting the animation automatically.

### Using ResourceReadySignal
The `ResourceReadySignal()` emits when the image data has been fully decoded and the view is ready to render its first frame.

```cpp
void InitializeAnimation(Dali::Ui::AnimatedImageView& view)
{
  view.ResourceReadySignal().Connect([](Dali::Ui::AnimatedImageView& animatedView) {
    // Animation is now loaded and ready
    animatedView.Play();
  });
}
```

> Note: Always check the `GetLoadingStatus()` method if you need to determine the state of the image resource outside of the signal callback.

## Best Practices for Performance

Running multiple high-resolution animations simultaneously can significantly impact memory usage and GPU performance. Follow these guidelines to keep your application performant:

* **Pause hidden animations:** Use the `Pause()` method when an `[AnimatedImageView](./animated-image-view.md)` is moved off-screen or hidden within a navigation stack to conserve processing cycles.
* **Release resources:** If an animation is no longer required for an extended period, clear the `ResourceUrl` or destroy the component to free up the internal frame buffers.
* **Limit concurrent frames:** Keep the number of active `[AnimatedImageView](./animated-image-view.md)` components on a single screen to a minimum to avoid exceeding the memory footprint allocated for texture decoding.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animated-image-view)
