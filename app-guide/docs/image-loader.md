---
id: image-loader
title: "image-loader"
sidebar_label: "image-loader"
---
## Introduction to [image-loader](./image-loader.md)

The DALi `image-loader` framework provides a comprehensive suite of tools for importing, managing, and preparing image data for application [rendering](./rendering.md). By abstracting the complexities of file system access, network retrieval, and pixel buffer management, it allows developers to efficiently integrate visual assets into their UI scenes.

This framework is distinct due to its dual-path architecture, offering both non-blocking asynchronous loading for maintaining frame-rate stability and high-performance synchronous operations for immediate resource availability. It serves as the bridge between raw data sources—such as disk files, network streams, or frame buffers—and the DALi visual system.

## Handling Image Paths with ImageUrl

The `ImageUrl` class acts as a handle-based wrapper for image resources, allowing developers to treat diverse data sources—such as textures, encoded buffers, and native interfaces—as unified objects. It is the standard type used to pass image references to DALi's visual components.

### Creating an ImageUrl
The `ImageUrl` class provides static `New` methods to instantiate a URL from existing `Texture` or `EncodedImageBuffer` objects. This allows the application to bridge existing resource handles into a format compatible with the [rendering](./rendering.md) system.

```cpp
// Example: Creating an ImageUrl from an encoded buffer
Dali::EncodedImageBuffer myBuffer = GetEncodedBuffer(); 
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(myBuffer);

// The imageUrl can now be used for setting properties in visuals.
```

> Note: `ImageUrl` objects are handle-based. Copying an `ImageUrl` simply copies the internal handle, not the underlying image data, making them lightweight for pass-by-value usage.

## Image Processing Utilities

The `ImageUrlUtils` namespace provides a robust set of helper functions to convert various internal memory structures into renderable URL handles. These utilities are essential when you need to display dynamic content, such as generated frame buffers or custom pixel arrays, within your application UI.

### Generating URLs from Dynamic Sources
You can use `ImageUrlUtils` to convert raw data sources like `[FrameBuffer](./frame-buffer.md)` or `PixelData` into an `ImageUrl`. This is particularly useful for real-time effects or post-processing results.

```cpp
// Example: Generating a URL from a FrameBuffer
Dali::FrameBuffer myFrameBuffer = GetFrameBuffer();
Dali::Ui::ImageUrl generatedUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(
    myFrameBuffer, 
    Dali::Pixel::RGBA8888, 
    1920, 
    1080
);
```

## Sub-Components Overview

The `[image-loader](./image-loader.md)` family is divided into three functional areas: asynchronous loading, synchronous loading, and resource management.

### AsyncImageLoader
`[AsyncImageLoader](./async-image-loader.md)` provides a non-blocking mechanism to load images from URLs into a worker thread, preventing UI thread stalls during network or high-resolution disk fetches.
→ See: [AsyncImageLoader documentation page]

### SyncImageLoader
`[SyncImageLoader](./sync-image-loader.md)` allows for immediate, blocking image loading, which is ideal for small, local assets that must be available instantly during application startup or state transitions.
→ See: [SyncImageLoader documentation page]

### TextureManager
`[TextureManager](./texture-manager.md)` handles the caching and lifecycle of loaded textures to ensure that duplicate image requests do not result in redundant memory consumption.
→ See: [TextureManager documentation page]

## Best Practices for Image Loading

Choosing the correct loading strategy is vital for maintaining a responsive user interface. Follow these guidelines to optimize your application's performance:

1. **Prefer Asynchronous Loading:** Always use `[AsyncImageLoader](./async-image-loader.md)` for remote resources or large images. This prevents the application from dropping frames while waiting for I/O operations to complete.
2. **Synchronous Limits:** Reserve `[SyncImageLoader](./sync-image-loader.md)` only for critical, small-sized assets located on local storage. Blocking the main thread should be avoided at all costs to ensure smooth interactions.
3. **Handle Cancellation:** When using `[AsyncImageLoader](./async-image-loader.md)`, always track the `loadingTaskId`. If a UI element is removed from the stage before its image finishes loading, call `Cancel(loadingTaskId)` to save system resources.

```cpp
// Example: Properly initiating an asynchronous load
Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::New();
uint32_t taskId = loader.Load("http://example.com/image.jpg");

// Connect to the signal to handle the result
loader.ImageLoadedSignal().Connect([](uint32_t id, const Dali::PixelData& data) {
    // Process the loaded data here
});
```

> Warning: Frequent calls to synchronous load operations on the main thread will cause visual stutters. If you find your app is non-responsive during image transitions, migrate those calls to `[AsyncImageLoader](./async-image-loader.md)`.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/image-loader)
