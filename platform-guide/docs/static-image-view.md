---
id: static-image-view
title: "ImageView"
sidebar_label: "ImageView"
---
## Introduction to StaticImageView

`StaticImageView` is a specialized component within the DALi UI framework designed specifically for the high-performance display of non-animated image assets. By utilizing a optimized [rendering](./rendering.md) path that avoids the overhead of [animation](./animation.md) decoders, it provides a lightweight solution for [rendering](./rendering.md) static bitmaps, textures, and UI icons within your view hierarchy.

You should use `StaticImageView` whenever your application requires the display of static media, such as background wallpapers, profile pictures, or icon assets. It is distinct from `AnimatedImageView` because it prioritizes memory footprint and frame-rate stability by omitting playback control logic, making it the preferred choice for static UI elements. → See: [[AnimatedImageView](./animated-image-view.md)]

## Component Lifecycle and Threading

The `StaticImageView` lifecycle is managed through the DALi core, which orchestrates the asynchronous loading and decoding of image data. When a URL is assigned, the engine offloads the resource retrieval to a dedicated background image-loading thread, ensuring the main UI loop remains responsive during I/O-intensive operations.

The component is created using the `New()` factory method, which initializes the internal view handle. Once added to the scene, the view monitors the status of its assigned resource; the actual texture upload to the GPU occurs on the render thread once the decoding process is complete.

```cpp
// Example: Creating and adding a StaticImageView to the scene
void CreateImage(Dali::Ui::View parent)
{
  Dali::Ui::StaticImageView myImage = Dali::Ui::StaticImageView::New("my_image.png");
  
  // Set basic view properties (inherited via View/Actor)
  myImage.SetSize(200.0f, 200.0f);
  
  parent.Add(myImage);
}
```

## Resource Management and URL Configuration

Efficient management of image URLs is critical for minimizing application memory consumption. The `SetResourceUrl` method allows you to define the source of the image, while `GetResourceUrl` provides a mechanism to verify the current configuration.

> Note: Changing the URL after initialization will trigger a re-load sequence. Ensure that old resources are properly cleared if memory usage spikes in your specific use case.

```cpp
// Example: Updating the resource URL dynamically
void UpdateIcon(Dali::Ui::StaticImageView& imageView, const Dali::String& newUrl)
{
  if (imageView.GetResourceUrl() != newUrl)
  {
    imageView.SetResourceUrl(newUrl);
  }
}
```

## Styling and Visual Transformation

`StaticImageView` provides a direct interface for applying color-based stylistic changes via `SetImageColor`. This method applies a color multiplier (tint) to the texture, which is particularly useful for highlighting selected states or applying brand-specific color themes to monochromatic assets without requiring separate image files.

```cpp
// Example: Applying a tint to the static image
void HighlightImage(Dali::Ui::StaticImageView& imageView)
{
  // Sets the image tint to a semi-transparent blue
  imageView.SetImageColor(Dali::Ui::UiColor(0.0f, 0.0f, 1.0f, 0.5f));
  
  // Verify the color
  Dali::Ui::UiColor currentColor = imageView.GetImageColor();
}
```

## Handling Resource Availability

To ensure a seamless user experience, you must synchronize application state with the image loading process using the `ResourceReadySignal`. This signal is emitted once the image has been fully decoded and is ready to be rendered to the screen.

```cpp
// Example: Connecting to the ResourceReadySignal
void SetupSignal(Dali::Ui::StaticImageView& imageView)
{
  imageView.ResourceReadySignal().Connect([](Dali::Ui::StaticImageView& view) {
    // Image is now ready, perform transition animations or UI updates here
    view.SetOpacity(1.0f);
  });
  
  // Initially hide the view until it is ready
  imageView.SetOpacity(0.0f);
}
```

## Performance Optimization Patterns

To maintain high performance in memory-constrained environments, `StaticImageView` should be treated as a lightweight handle. When passing components between internal logic layers, use `DownCast` to safely recover the `StaticImageView` type from generic `BaseHandle` references.

*   **Minimizing Draw Calls:** Reuse `StaticImageView` instances where possible rather than destroying and recreating them during frequent UI updates.
*   **DownCasting:** Always validate your handles using `DownCast` when retrieving components from parent-child view traversals to prevent invalid pointer access.
*   **Loading Status:** Use `GetLoadingStatus()` to check for failures (e.g., `ResourceStatus::FAILED`) and implement retry logic or placeholder display accordingly.

```cpp
// Example: Safe downcasting and status checking
void ConfigureView(Dali::BaseHandle handle)
{
  Dali::Ui::StaticImageView view = Dali::Ui::StaticImageView::DownCast(handle);
  
  if (view && view.GetLoadingStatus() == Dali::Ui::Visual::ResourceStatus::READY)
  {
    // Proceed with view logic
  }
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/static-image-view)
