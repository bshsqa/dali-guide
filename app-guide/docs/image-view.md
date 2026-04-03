---
id: image-view
title: "image-view"
sidebar_label: "image-view"
---
## Introduction to Image View

The Image View family provides a robust, high-performance mechanism for [rendering](./rendering.md) visual assets within a DALi application. These components are built upon `Dali::Ui::View`, allowing them to integrate seamlessly into your UI hierarchy while inheriting standard transform and event capabilities.

By utilizing dedicated view classes, developers can display static, animated, or complex vector graphics without manually managing low-level [rendering](./rendering.md) pipelines. Each view is optimized for its specific content type, ensuring efficient resource consumption and smooth UI transitions.

## Sub-Components Overview

The Image View framework is composed of specialized components designed to handle distinct media formats.

*   **Static Image View**: The standard component for displaying raster graphics such as PNG, JPG, or SVG files. → See: `StaticImageView` (Note: This refers to `Dali::Ui::ImageView`).
*   **Animated Image View**: Specialized for frame-based animations, primarily used for GIF playback. → See: `AnimatedImageView`.
*   **Lottie Animation View**: A dedicated viewer for high-fidelity, resolution-independent vector animations. → See: `LottieAnimationView`.

## Configuration and Resource Handling

Effective resource management is critical to maintaining high frame rates and avoiding memory pressure. These components support asynchronous loading and lifecycle notifications to ensure your UI remains responsive during asset fetching.

### Managing Image Resources

The `SetResourceUrl` method is the primary way to define the content of an image-based view. The framework handles the underlying IO and caching automatically.

```cpp
// Example: Creating and configuring an ImageView
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();
imageView.SetResourceUrl("resources/my_image.png");

// Add to the main UI View hierarchy
this->Add(imageView);
```

### Resource Loading Status

You can monitor the readiness of an image to perform post-load UI logic, such as fading in the image only after it has successfully decoded.

```cpp
// Example: Using the ResourceReadySignal
imageView.ResourceReadySignal().Connect([](Dali::Ui::ResourceReadySignalType& signal) {
    // Logic to execute when the image is fully loaded and ready
});
```

> **Note:** The `GetLoadingStatus()` method provides a synchronous check of the current state (`Ui::Visual::ResourceStatus`), which is useful for debugging or conditional logic during the view's lifecycle.

## Layout and Visual Customization

The Image View provides properties to control how your assets are scaled, sampled, and clipped within their parent bounds.

### Controlling Display Geometry

Use `SetFitSizeToImage` to ensure the view's dimensions match the natural aspect ratio of the source asset, or use `SetPixelArea` to display only a specific sub-region of an image.

```cpp
// Example: Fitting the view to the image dimensions
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();
imageView.SetResourceUrl("assets/icon.png");
imageView.SetFitSizeToImage(true); // View will resize based on the icon's natural size
```

### Rendering Quality

For high-quality scaling, you can control the `SamplingMode` to determine how the engine interpolates pixels when the source image is scaled down or up.

```cpp
// Example: Configuring sampling for sharp output
imageView.SetSamplingMode(Dali::Ui::SamplingMode::Box); 
```

## Common View Patterns

When working with `Dali::Ui::[ImageView](./static-image-view.md)` or `Dali::Ui::[AnimatedImageView](./animated-image-view.md)`, you often need to manipulate the visual presentation through color multipliers or animation state control.

### Applying Color Multipliers

The `SetImageColor` method applies a color tint or multiplier to the texture. This is commonly used for dynamic UI themes or highlighting selected items.

```cpp
// Example: Tinting an image to blue
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New("image.png");
imageView.SetImageColor(Dali::Ui::UiColor(0.0f, 0.0f, 1.0f, 1.0f));
```

### Controlling Animated Content

`[AnimatedImageView](./animated-image-view.md)` provides specific control over playback, allowing you to `Play()`, `Pause()`, or `Stop()` the animation based on user interaction or scene changes.

```cpp
// Example: Controlling an animation
Dali::Ui::AnimatedImageView animView = Dali::Ui::AnimatedImageView::New("animation.gif");
animView.SetLoopCount(5); // Play 5 times
animView.Play();          // Begin playback

// Later, pause the animation
animView.Pause();
```

> **Warning:** Excessive calls to `Reload()` on an `[ImageView](./static-image-view.md)` can lead to disk IO bottlenecks. Only call `Reload()` when the source file has genuinely changed on the filesystem.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/image-view)
