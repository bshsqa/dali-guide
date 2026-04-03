---
id: image-view
title: "image-view"
sidebar_label: "image-view"
---
## Introduction to Image View

The DALi `image-view` module provides a comprehensive suite of UI components designed for the efficient display and management of static and dynamic graphical assets. By leveraging the `Dali::Ui::View` architecture, these components abstract the complexities of hardware-accelerated image decoding and scene graph integration into a high-level, declarative API.

Developers should utilize these views whenever graphical content—ranging from simple static icons to complex animated formats like GIFs—needs to be rendered within the application UI. What makes the `image-view` family distinct is its separation of visual content representation from layout logic, ensuring that image loading, decoding, and memory management remain decoupled from the View's position and transformation properties.

## Component Architecture and Integration

The `image-view` architecture operates on a bridge pattern, where the public `Dali::Ui::View` handle manages the high-level application lifecycle, while internal implementation classes (such as `AnimatedImageViewImpl`) handle the heavy lifting of resource loading and GPU synchronization. This structure ensures that main-thread responsiveness is maintained by offloading image processing to the engine's background task executors.

When a developer interacts with these classes, they are modifying properties that trigger synchronization [signals](./signals.md) sent to the DALi render thread. Lifecycle management is automatic; when a `View` is removed from the scene or destroyed, the associated internal `Impl` [object](./object.md) initiates the release of hardware resources, preventing memory leaks in resource-intensive graphical applications.

## Image Rendering and Configuration Policies

Image [rendering](./rendering.md) within the `image-view` module is governed by specific policies that determine how assets are buffered and displayed. While these views inherit transform properties from `Dali::Ui::View`, they specifically introduce configuration hooks for resource targeting.

> Note: Image resources are processed asynchronously. Accessing the loading status immediately after setting a URL may return a "loading" state; always connect to the `ResourceReadySignal` for logic dependent on the asset being fully decoded.

## Sub-Components Overview

The `image-view` family is composed of specialized views, each tailored to specific asset types and playback requirements:

* **[ImageView](./static-image-view.md)**: The base class for displaying standard static image formats. → See: [StaticImageView]
* **[AnimatedImageView](./animated-image-view.md)**: Specialized for time-based image sequences, such as GIF files, providing playback control. → See: [[AnimatedImageView](./animated-image-view.md)]
* **[LottieAnimationView](./lottie-animation-view.md)**: A high-performance renderer for vector-based JSON animations. → See: [[LottieAnimationView](./lottie-animation-view.md)]

### [AnimatedImageView](./animated-image-view.md)

The `AnimatedImageView` is the primary interface for playing back multi-frame image formats. It manages the [animation](./animation.md) state machine, allowing developers to play, pause, and loop content programmatically.

**Example Usage:**

```cpp
#include <dali-ui/dali-ui.h>

void CreateAnimatedView(Dali::Ui::View parent)
{
    // Create the animated view
    auto animView = Dali::Ui::AnimatedImageView::New("path/to/animation.gif");
    
    // Configure playback properties
    animView.SetLoopCount(5);
    
    // Connect to readiness signal
    animView.ResourceReadySignal().Connect([](auto& source) {
        // Now safe to start playback
        static_cast<Dali::Ui::AnimatedImageView&>(source).Play();
    });
    
    // Add to parent view
    parent.Add(animView);
}
```

The methods `Play()`, `Pause()`, and `Stop()` provide direct control over the internal implementation's frame clock. Using `SetResourceUrl()` will automatically trigger an internal reset of the animation state, ensuring that if a new asset is assigned, the view correctly transitions to the initial frame of the new source.

## Integration API and Native Implementation

For advanced platform developers extending the engine's rendering capabilities, the `Integration` namespace exposes internal implementation classes. These classes, such as `AnimatedImageViewImpl`, provide the low-level hooks required to interface with custom decoding pipelines or platform-specific image providers.

### Internal Implementation Patterns

The `AnimatedImageViewImpl` class is the reference-counted engine-side object that mirrors the public `[AnimatedImageView](./animated-image-view.md)` handle. Custom extensions should inherit from these `Impl` classes only when modifying the core rendering loop or implementing new animation file-format parsers.

> Warning: Directly interacting with `Impl` classes requires strict adherence to reference counting. Objects of this type must only be managed via the provided `New()` factory methods to ensure the DALi ownership model remains consistent.

**Customizing Implementation:**

```cpp
// Example of accessing implementation logic (Internal usage only)
void InitializeCustomView()
{
    // Creating the implementation layer directly
    auto impl = Dali::Ui::Integration::AnimatedImageViewImpl::New();
    impl->SetResourceUrl("custom_format.bin");
    impl->Play();
}
```

The `Integration::AbsoluteLayoutManager` and its associated `AbsoluteLayoutImpl` define how these image-view components are positioned when not using flexible box or constraint-based systems. By overriding `Measure` and `ArrangeChildren`, developers can dictate exact pixel-level offsets for [images](./images.md) within a custom view container.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/image-view)
