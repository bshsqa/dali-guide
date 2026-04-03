---
id: animated-image-view
title: "AnimatedImageView"
sidebar_label: "AnimatedImageView"
---
## Introduction to [AnimatedImageView](./animated-image-view.md)

The `AnimatedImageView` is a specialized `View` component designed to render and control sequence-based or multi-frame image formats, such as animated GIFs. It is the preferred choice over standard static image views when you need to display dynamic visual content that requires frame-by-frame orchestration within the DALi UI hierarchy.

By encapsulating the decoding and playback logic, `AnimatedImageView` allows developers to manage complex animations with a simple, imperative API, abstracting away the underlying frame-buffer updates and synchronization requirements.

## Component Lifecycle and Initialization

`AnimatedImageView` follows the DALi `Handle` lifecycle pattern. Since it inherits from `Dali::Ui::View`, it is a lightweight [object](./object.md) that points to a backend implementation, making copy and assignment operations performant and safe.

### Creating an Instance

To initialize a new `AnimatedImageView`, you should use the static `New()` method. This ensures the underlying engine resources are properly allocated before the view is added to the scene.

```cpp
#include <dali-ui/dali-ui.h>

void CreateAnimatedView(Dali::Ui::View parent)
{
  // Create an initialized AnimatedImageView instance
  Dali::Ui::AnimatedImageView animatedView = Dali::Ui::AnimatedImageView::New("my_animation.gif");
  
  // Add it to the UI hierarchy
  parent.Add(animatedView);
}
```

> **Note:** The default constructor `[AnimatedImageView](./animated-image-view.md)()` creates an empty handle. Always check if a handle is empty using the implicit boolean operator before invoking methods to avoid crashes.

## Resource Management and Loading

Efficient resource management is handled via URL-based loading. Because animations can be memory-intensive, DALi loads resources asynchronously to prevent blocking the UI main loop.

### Setting and Monitoring Resources

Use `SetResourceUrl` to define the image source. To react to the completion of the loading process, subscribe to the `ResourceReadySignal()`.

```cpp
void SetupImage(Dali::Ui::AnimatedImageView& view)
{
  view.SetResourceUrl("loading_spinner.gif");
  
  // Connect to the ready signal to start animation only after the file is parsed
  view.ResourceReadySignal().Connect([](Dali::Ui::AnimatedImageView& source) {
    source.Play();
  });
}
```

The `GetLoadingStatus()` method allows you to poll the `Dali::Ui::Visual::ResourceStatus` at any time, which is useful for conditional logic in complex UI states.

## Playback Control Interface

The playback interface provides full control over the animation's temporal state. These methods are non-blocking and affect the rendering pipeline immediately.

### Control Methods

- `Play()`: Starts or resumes the animation from its current frame.
- `Pause()`: Freezes the animation at the current frame.
- `Stop()`: Halts playback and resets the view to the initial frame.

### Loop Configuration

You can define the behavior of the animation cycle using `SetLoopCount`. Passing a specific integer defines the iteration count, whereas standard API implementation usually supports a value representing infinite loops.

```cpp
void ConfigurePlayback(Dali::Ui::AnimatedImageView& view)
{
  // Set to loop 3 times
  view.SetLoopCount(3);
  
  // Begin the animation
  view.Play();
}
```

## Visual Styling and Color Manipulation

`[AnimatedImageView](./animated-image-view.md)` supports color tinting or filtering, which is applied as a multiplier to the source image pixels during the rasterization process.

### Color Manipulation

The `SetImageColor` method accepts a `UiColor` object. This is highly efficient as it is performed by the GPU, allowing you to change the "mood" of an animation (e.g., dimming or color-shifting) without reloading the source asset.

```cpp
void SetTint(Dali::Ui::AnimatedImageView& view)
{
  // Apply a semi-transparent blue tint
  Dali::Ui::UiColor blueTint(0.0f, 0.0f, 1.0f, 0.5f);
  view.SetImageColor(blueTint);
}
```

## Integration and Casting Patterns

Since `[AnimatedImageView](./animated-image-view.md)` is part of the `Dali::Ui::View` hierarchy, it is frequently passed through generic view containers. Use `DownCast` to safely recover the `[AnimatedImageView](./animated-image-view.md)` type from a generic `BaseHandle` or a base `View` pointer.

### Safe Downcasting

When retrieving a child view from a layout or parent container, always perform a `DownCast` to ensure you are interacting with the specialized `[AnimatedImageView](./animated-image-view.md)` API.

```cpp
void UpdateView(Dali::BaseHandle handle)
{
  Dali::Ui::AnimatedImageView view = Dali::Ui::AnimatedImageView::DownCast(handle);
  
  if(view)
  {
    // It is safe to call AnimatedImageView methods now
    view.Stop();
  }
}
```

→ See: [ImageView](image-view-details) for information on static image handling and base view properties.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animated-image-view)
