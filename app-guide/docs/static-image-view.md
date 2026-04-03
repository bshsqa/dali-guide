---
id: static-image-view
title: "ImageView"
sidebar_label: "ImageView"
---
## Introduction to StaticImageView

The `StaticImageView` component is the specialized `Dali::Ui::View` variant designed for [rendering](./rendering.md) single-frame, non-animated image assets such as PNG, JPG, or SVG files. It provides a lightweight and optimized path for displaying static content, ensuring that memory and processing power are not unnecessarily consumed by [animation](./animation.md)-related logic.

Use `StaticImageView` when your UI requirements involve static icons, photos, or decorative graphical elements. If your design requires GIF or multi-frame playback capabilities, consider using the sibling component. 

→ See: [[AnimatedImageView](./animated-image-view.md)]

## Creating and Initializing Views

The `StaticImageView` is instantiated through a factory method, which creates a handle to the underlying UI component and allows for immediate resource assignment. Proper initialization is the first step in ensuring the view is ready to be added to the UI scene graph.

### Using the New() Factory Method
The `New()` method is the standard way to create an instance. It optionally accepts a string URL, allowing you to create and configure the image source in a single line of code.

```cpp
#include <dali/ui/view/static-image-view.h>

// Initialize a StaticImageView with a specific resource URL
Dali::Ui::StaticImageView imageView = Dali::Ui::StaticImageView::New("path/to/image.png");

// Add to your main container (assuming 'this' is a container View)
this->Add(imageView);
```

> **Note:** The `New()` method returns a smart pointer handle. Ensure you maintain a reference to this handle if you need to modify the view later, as it is reference-counted and will be destroyed if all handles go out of scope.

## Configuring Image Resources

Efficient resource management is critical for a smooth user experience. The `StaticImageView` provides straightforward methods to update the displayed image dynamically during the application lifecycle.

### Setting and Getting the Resource URL
The `SetResourceUrl` method allows you to change the image content at runtime. This is useful for state-driven UI changes, such as swapping an icon when a button state changes.

```cpp
// Update the image source dynamically
imageView.SetResourceUrl("path/to/new_icon.png");

// Retrieve the current URL for logic verification
Dali::String currentUrl = imageView.GetResourceUrl();
```

## Customizing Visual Appearance

You can alter the visual presentation of a static image without modifying the source file. This is particularly useful for applying theme-based color changes to icons.

### Tinting with Image Color
The `SetImageColor` method applies a color multiplier (tint) to the image pixels. This is a non-destructive way to create variations of your assets, such as highlighting an icon in a specific brand color.

```cpp
// Tint the image blue
Dali::Ui::UiColor blueTint(0.0f, 0.0f, 1.0f, 1.0f);
imageView.SetImageColor(blueTint);

// Retrieve the current tint
Dali::Ui::UiColor color = imageView.GetImageColor();
```

## Handling Resource Loading States

Because image loading occurs asynchronously to keep the UI thread responsive, you may need to know when the asset is fully processed and ready to be painted on the screen.

### Using the ResourceReadySignal
The `ResourceReadySignal()` provides a callback mechanism that fires once the image data has been successfully decoded and is ready for rendering.

```cpp
// Define a callback function
void OnImageReady(Dali::Ui::StaticImageView& view)
{
    // The image is now fully loaded
}

// Connect the signal
imageView.ResourceReadySignal().Connect(&OnImageReady);
```

You can also check the current state synchronously using the `GetLoadingStatus()` method, which returns the current `Ui::Visual::ResourceStatus`.

## Common Patterns and Best Practices

To ensure optimal performance, keep the following patterns in mind when working with `StaticImageView`:

*   **Avoid Redundant Loading:** Only call `SetResourceUrl` when the content actually needs to change. Frequently swapping URLs can cause unnecessary disk I/O and memory pressure.
*   **Use Proper Scaling:** Before loading high-resolution images, ensure they are appropriately sized for their container. Loading a large high-resolution asset into a small view consumes unnecessary memory.
*   **Resource Cleanup:** When a `StaticImageView` is no longer needed, simply remove it from the parent container. The DALi memory management system will handle the release of the associated image resources as the handle count drops to zero. 
*   **Downcasting:** If you receive a generic `BaseHandle` from a signal or container child list that you know is a `StaticImageView`, use `StaticImageView::DownCast()` to safely retrieve the specific type handle.

```cpp
// Example of safe DownCast
Dali::BaseHandle child = this->GetChildAt(0);
Dali::Ui::StaticImageView imageView = Dali::Ui::StaticImageView::DownCast(child);

if (imageView) 
{
    // Successfully casted, safe to call methods
    imageView.SetImageColor(Dali::Ui::UiColor::RED);
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/static-image-view)
