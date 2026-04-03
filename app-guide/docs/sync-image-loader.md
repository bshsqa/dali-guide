---
id: sync-image-loader
title: "SyncImageLoader"
sidebar_label: "SyncImageLoader"
---
## Introduction to Sync Image Loader

The `AsyncImageLoader` (commonly referred to in application architecture as the sync image loader) is designed to handle image resource decoding off the main UI thread. By offloading resource-heavy pixel decoding, it prevents frame drops and ensures fluid interaction, even when loading multiple high-resolution assets from local storage or remote URLs.

Use the `AsyncImageLoader` when you need to load pixel data dynamically while maintaining application performance. Unlike simple image display components that manage their own resources, this loader provides fine-grained control over the loading lifecycle, allowing you to prioritize, cancel, or track specific image requests. 

→ See: [Image Loader (Parent Component)]

## Getting Started with Image Loading

Getting started requires initializing the loader instance and triggering a load request. The loader acts as an asynchronous processor, returning a unique identifier for each task that allows you to manage or cancel it later.

### Instantiating and Loading
To begin, call `AsyncImageLoader::New()` to create a handle. You can then invoke one of the `Load` overloads to initiate the task.

```cpp
using namespace Dali::Ui;

// Create the loader instance
AsyncImageLoader loader = AsyncImageLoader::New();

// Basic load request
uint32_t taskId = loader.Load("path/to/image.png");

// Advanced load request with dimensions and sampling
ImageDimensions dimensions(200, 200);
uint32_t customTaskId = loader.Load("path/to/image.png", 
                                    dimensions, 
                                    Dali::FittingMode::SCALE_TO_FIT, 
                                    SamplingMode::BOX_THEN_LINEAR, 
                                    true);
```

> Note: While the loader is designed for high performance, triggering an excessive volume of simultaneous `Load` requests may increase memory pressure; consider throttling requests if your UI logic requires rapid image switching.

## Managing Load Requests

Effective resource management involves monitoring active tasks and cleaning up when they are no longer necessary, such as when a user navigates away from a specific view. The loader provides methods to target specific tasks or clear the queue entirely.

### Cancelling Tasks
You can use `Cancel()` to stop a specific task by its ID or `CancelAll()` to wipe the entire pending queue.

```cpp
// Cancel a specific task using the ID returned from Load()
bool success = loader.Cancel(taskId);

// Clear all pending tasks if the UI component is destroyed
loader.CancelAll();
```

> Warning: `Cancel()` only successfully halts the task if it is still queuing or in the early stages of processing in the worker thread. If the task has already reached completion, the signal may still be emitted.

## Handling Completion Signals

Because the loading process happens off-thread, you must use signals to receive the resulting pixel data. By connecting to `ImageLoadedSignal()`, you gain access to the data once the worker thread has finished decoding.

### Connecting to the Signal
The `ImageLoadedSignal` notifies your application when a load task concludes. Your callback should handle the returned pixel data and verify the status of the operation.

```cpp
// Define a callback function matching the signal signature
void OnImageLoaded(uint32_t id, const Dali::PixelData& pixelData)
{
  // Process the loaded pixel data here
}

// Connect the callback to the loader
loader.ImageLoadedSignal().Connect(&OnImageLoaded);
```

## Best Practices and Performance

To maintain optimal frame rate stability, ensure your application logic does not block the main thread while waiting for image data. Use the asynchronous pattern provided by this loader rather than attempting manual decoding on the main thread.

* **Batching:** Use `CancelAll` when the parent view is hidden or destroyed to ensure that the worker thread is not wasting cycles on images that are no longer visible.
* **Sampling:** Always provide explicit `ImageDimensions` when loading images that are larger than their intended display size to reduce memory usage and improve decoding speed.
* **Threading:** Remember that all callbacks connected to `ImageLoadedSignal` will be triggered on the main thread, making them safe for updating UI components like `[ImageView](./static-image-view.md)` directly. 

> Note: Platform-level details regarding memory cache limits and hardware-accelerated texture formats are managed by the internal DALi [rendering](./rendering.md) pipeline. Refer to the platform guide for memory optimization strategies.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/sync-image-loader)
