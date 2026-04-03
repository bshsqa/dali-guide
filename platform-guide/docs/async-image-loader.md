---
id: async-image-loader
title: "AsyncImageLoader"
sidebar_label: "AsyncImageLoader"
---
## Overview of [AsyncImageLoader](./async-image-loader.md)

The `AsyncImageLoader` is a specialized component within the DALi framework designed to load pixel data from a URL without blocking the main application thread. It serves as the primary mechanism for offloading heavy image decoding tasks to background worker threads, ensuring that the GUI remains responsive during I/O-intensive operations.

You should use `AsyncImageLoader` when your application needs to fetch [images](./images.md) from the local file system or network and process them into a format suitable for GPU textures. Unlike synchronous loading methods, `AsyncImageLoader` is distinct because it returns a unique task identifier immediately, allowing you to manage multiple concurrent requests and listen for completion [signals](./signals.md) asynchronously.

→ See: `ImageLoader`

## Lifecycle and Memory Management

`AsyncImageLoader` follows the standard DALi handle-based memory management pattern. As a handle class, it acts as a lightweight pointer to an underlying internal [object](./object.md), meaning you can safely copy or assign it without duplicating the heavy loading state.

The lifecycle is initiated by calling the static `New()` method, which allocates the necessary resources for background thread communication. Because the class manages internal decoding buffers and task queues, it is essential to ensure the handle remains in scope for the duration of the loading process. When the last handle to the `AsyncImageLoader` is destroyed, the internal state is cleaned up, although pending tasks may be implicitly cancelled depending on the engine's current state.

```cpp
// Correct way to initialize and manage an AsyncImageLoader
Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::New();

// The handle can be safely passed around or stored
void MyApplication::OnInitialize() {
  mLoader = Dali::Ui::AsyncImageLoader::New();
}
```

## Loading Operations and Configuration

The `Load` method is the primary entry point for requesting image processing. It triggers an asynchronous task that reads the specified URL, decodes the image, and—if specified—applies scaling and orientation adjustments before delivering the result.

The API provides three overloads of `Load`, ranging from simple path-based requests to granular control over decoding parameters.

### Configuring Load Requests
When calling `Load`, you can define the target `ImageDimensions` to downsample images during the decoding phase. You may also specify `FittingMode` and `SamplingMode` to control the visual quality and performance trade-offs during resize operations.

*   `url` (const std::string&): The file path or network URI of the image.
*   `dimensions` (`ImageDimensions`): The target size to which the image should be decoded.
*   `fittingMode` (`Dali::FittingMode::Type`): Determines how the image fits into the requested dimensions (e.g., `SCALE_TO_FIT`).
*   `samplingMode` (`SamplingMode::Type`): Defines the algorithm used to sample pixels (e.g., `BOX_THEN_LINEAR`).
*   `orientationCorrection` (bool): If true, the loader automatically applies rotation metadata found in image headers (e.g., EXIF).

Returns: A `uint32_t` representing the `loadingTaskId`, which is required to cancel the operation if needed.

```cpp
// Example: Requesting a thumbnail with specific quality settings
uint32_t taskId = mLoader.Load(
    "path/to/image.jpg", 
    Dali::ImageDimensions(200, 200), 
    Dali::FittingMode::SCALE_TO_FIT, 
    Dali::SamplingMode::BOX_THEN_LINEAR, 
    true
);
```

> Note: If you do not provide dimensions, the loader will use the image's native resolution, which may lead to high memory consumption. Always specify dimensions when loading large assets.

## Concurrency and Thread Safety

The `[AsyncImageLoader](./async-image-loader.md)` is built on a multi-threaded architecture where the main UI thread acts as the coordinator and a worker pool handles the file I/O and CPU-intensive decoding. 

All public API methods are thread-safe and must be called from the main thread. When a background task completes, the `[AsyncImageLoader](./async-image-loader.md)` automatically marshals the result back to the main thread before emitting the `ImageLoadedSignal`. This ensures that any subsequent UI updates (like creating a `Texture` or `[ImageView](./static-image-view.md)`) occur safely within the context of the main rendering loop.

## Signal Handling and Result Delivery

To retrieve the decoded pixel data, you must connect a callback function to the `ImageLoadedSignal`. This signal notifies the application as soon as an image request has been processed, whether successfully or via an error state.

The signal provides the result data, which includes the `loadingTaskId` to help you identify which specific request has finished.

```cpp
// Connecting the signal
mLoader.ImageLoadedSignal().Connect(this, &MyApplication::OnImageLoaded);

// Callback signature
void MyApplication::OnImageLoaded(uint32_t id, const Dali::PixelData& pixelData) {
    if(pixelData) {
        // Successfully loaded: Create a Texture or display the image
    } else {
        // Handle error cases
    }
}
```

## Cancellation Mechanisms

Managing queue pressure is vital in applications that load images rapidly, such as scrolling lists or grid views. If a user scrolls past an image before it finishes loading, you should explicitly cancel that request to free up CPU and memory resources.

*   `Cancel(uint32_t loadingTaskId)`: Attempts to remove a specific task from the queue. Returns `true` if the cancellation was successful.
*   `CancelAll()`: Immediately clears all pending tasks currently sitting in the worker queue.

```cpp
// Cancel a specific task when an element is removed from the view
void MyApplication::OnElementRemoved(uint32_t taskId) {
    bool cancelled = mLoader.Cancel(taskId);
    if(cancelled) {
        // Cleanup resources related to this task
    }
}
```

> Warning: `Cancel` can only stop tasks that are still waiting in the queue. If a task has already entered the decoding phase on the worker thread, it may still complete, though the result will be discarded by the loader and the signal will not be emitted.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/async-image-loader)
