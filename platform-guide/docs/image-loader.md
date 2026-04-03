---
id: image-loader
title: "image-loader"
sidebar_label: "image-loader"
---
## Introduction to [image-loader](./image-loader.md)

The `image-loader` module is the primary gateway for bringing external image resources into the DALi [rendering](./rendering.md) engine. It provides a robust, thread-aware infrastructure for fetching, decoding, and processing [images](./images.md) from diverse sources, including local storage and network locations, while maintaining UI responsiveness.

Developers should use the `image-loader` whenever they need to display dynamic or static [images](./images.md) in their application. It is distinct because it abstracts away complex file decoding and asynchronous [threading](./threading.md), offering a unified `ImageUrl` abstraction that ensures seamless compatibility across various visual components in the DALi framework.

## Internal Architecture and Lifecycle

The `image-loader` utilizes a multi-threaded architecture to decouple heavy I/O and decoding operations from the main application thread. When an image request is submitted, the engine offloads the decoding process to a background worker thread, ensuring the UI remains performant and fluid.

The lifecycle of an image request begins with a `Load` call, which returns a unique task identifier. Once the background process completes, the engine dispatches a signal to the main thread, providing the decoded pixel data. This structured approach allows developers to handle image availability callbacks without managing platform-specific thread synchronization primitives manually.

## Sub-Components Overview

The `image-loader` family consists of three specialized components designed to handle different loading scenarios and resource lifecycle management.

* **[AsyncImageLoader](./async-image-loader.md)**: Provides an asynchronous interface for loading [images](./images.md) in the background, preventing main-thread blocking. → See: [[AsyncImageLoader](./async-image-loader.md)]
* **[SyncImageLoader](./sync-image-loader.md)**: Facilitates immediate, blocking image loading for scenarios where synchronous processing is required (note: typically used only when startup latency is acceptable). → See: [[SyncImageLoader](./sync-image-loader.md)]
* **[TextureManager](./texture-manager.md)**: An internal engine component responsible for the efficient caching and lifecycle management of loaded textures to prevent redundant memory consumption. → See: [[TextureManager](./texture-manager.md)]

## Platform Integration Guidelines

When integrating the [image-loader](./image-loader.md) into custom pipelines, always prefer asynchronous operations to maintain a high frame rate. Ensure that all loading tasks are tracked via their `loadingTaskId`, which is essential for canceling obsolete requests, such as those triggered by scrolling lists where the item has left the viewport.

> Note: Image loading involves I/O operations and memory allocation. Always monitor the memory footprint when loading multiple high-resolution [images](./images.md) simultaneously, as the `AsyncImageLoader` will queue these tasks in the background.

## Devel-API and Customization

The `DevelAsyncImageLoader` provides advanced, lower-level control over the loading pipeline, which is particularly useful for complex graphical effects and specialized asset handling. This includes features like pre-multiplied alpha channel processing and support for animated image sequences.

### Advanced Loading with Pre-multiplication

The `DevelAsyncImageLoader::Load` method allows for explicit control over whether the alpha channel should be multiplied into color channels during the decode stage.

```cpp
// Example: Loading an image with explicit pre-multiplication
Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::New();
Dali::ImageDimensions dimensions(200, 200);

uint32_t taskId = Dali::Ui::DevelAsyncImageLoader::Load(
    loader,
    "path/to/image.png",
    dimensions,
    Dali::FittingMode::SCALE_TO_FILL,
    Dali::SamplingMode::BOX_THEN_LINEAR,
    true, // orientationCorrection
    Dali::Ui::DevelAsyncImageLoader::PreMultiplyOnLoad::TRUE
);
```

### Applying Masks

The `ApplyMask` function in the `DevelAsyncImageLoader` namespace allows developers to combine a source pixel buffer with a mask buffer, enabling dynamic shape clipping or complex transparency effects during the loading phase.

## Common Utilities and Resource Handling

The `ImageUrl` class and `ImageUrlUtils` namespace provide a consistent way to handle image references, regardless of their origin. An `ImageUrl` acts as a descriptor that can be consumed by DALi visuals, effectively bridging the gap between raw pixel data and rendered output.

### Using ImageUrlUtils

`ImageUrlUtils` contains static helpers for converting various buffers—such as `[FrameBuffer](./frame-buffer.md)`, `PixelData`, or `EncodedImageBuffer`—into a standardized `ImageUrl` object. This is essential when creating custom visuals that need to display dynamically generated content.

```cpp
#include <dali/dali.h>
#include <dali/ui/image-loader/image-url-utils.h>

// Generating a URL from existing PixelData
void CreateVisualFromPixels(Dali::PixelData pixelData) {
    // Generate the URL for the visual system
    Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(pixelData, false);
    
    // The imageUrl can now be used as a source for a visual
    std::string urlString = imageUrl.GetUrl();
}
```

> Warning: Always ensure that the data source (e.g., `PixelData` or `[FrameBuffer](./frame-buffer.md)`) remains valid for as long as the generated `ImageUrl` is in use by the [rendering](./rendering.md) system. Destroying the underlying buffer prematurely will lead to [rendering](./rendering.md) errors or undefined behavior.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/image-loader)
