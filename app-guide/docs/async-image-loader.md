---
id: async-image-loader
title: "AsyncImageLoader"
sidebar_label: "AsyncImageLoader"
---
## Introduction to [AsyncImageLoader](./async-image-loader.md)

The `AsyncImageLoader` provides a dedicated mechanism for fetching image resources from local storage or remote URLs without blocking the main event loop. By offloading resource decoding and data processing to a background worker thread, it prevents UI stuttering and ensures your application remains responsive during intensive I/O operations.

You should prioritize `AsyncImageLoader` over synchronous loading whenever your application needs to display [images](./images.md) from the network or high-resolution local files. It is particularly essential for dynamic lists or views where [images](./images.md) must be loaded on-demand, as it allows for asynchronous completion signaling and individual request cancellation.

## Initializing the Loader

To begin using the asynchronous loading functionality, you must instantiate the [object](./object.md) using the provided factory method. The `AsyncImageLoader` follows DALi's handle-based architecture, making it lightweight to pass around and manage.

### Creating an Instance
The `New()` static method creates a new instance of the loader, which manages the background worker threads required for asynchronous operations.

```cpp
#include <dali/dali.h>
#include <dali/devel-api/adaptor-framework/image-loading.h>

// Initialize the loader
Dali::Ui::AsyncImageLoader myLoader = Dali::Ui::AsyncImageLoader::New();
```

> Note: `[AsyncImageLoader](./async-image-loader.md)` is a handle class. Copying the handle does not copy the loader instance itself; instead, it creates a new reference to the same underlying object.

## Loading Images Asynchronously

The `Load()` method initiates the image fetching process. Depending on your requirements, you can either use default settings or specify precise dimensions, fitting, and sampling modes to optimize memory usage.

### Initiating a Load Request
The `Load()` method triggers the background operation and immediately returns a unique `uint32_t` identifier for that specific task. This identifier is crucial for tracking or canceling the request later.

```cpp
// Basic load using default settings
uint32_t taskId = myLoader.Load("http://example.com/image.png");

// Advanced load with specific dimensions and sampling
Dali::ImageDimensions dimensions(200, 200);
uint32_t customTaskId = myLoader.Load(
    "file:///local/path/image.jpg", 
    dimensions, 
    Dali::FittingMode::SCALE_TO_FILL, 
    Dali::SamplingMode::BOX_THEN_LINEAR, 
    true);
```

- `url`: A string representing the image path or network resource.
- `dimensions`: An `ImageDimensions` object defining the target size.
- `fittingMode`: Defines how the image fits into the target dimensions (e.g., `SCALE_TO_FILL`).
- `samplingMode`: Determines how pixels are sampled during resizing.
- `orientationCorrection`: Boolean to toggle automatic orientation fixing based on metadata.

## Handling Completion Signals

Once the worker thread finishes processing the image, the `[AsyncImageLoader](./async-image-loader.md)` notifies the application via the `ImageLoadedSignal()`. This is where you connect your callback to process the resulting data.

### Connecting to the Signal
You must connect a callback function to the signal to receive the result of your load request. The signal provides the task ID and the result data, allowing you to update your UI accordingly.

```cpp
// Define the callback function
void OnImageLoaded(uint32_t id, const Dali::PixelData& data)
{
    // Apply the pixel data to your UI component
}

// Connect to the signal
myLoader.ImageLoadedSignal().Connect(&OnImageLoaded);
```

> Warning: Always ensure that the callback logic is thread-safe or properly marshaled back to the main thread if you are performing complex UI updates directly within the handler.

## Managing Load Requests

In dynamic applications, such as a scrolling list, users may scroll past items faster than they can load. The `Cancel()` and `CancelAll()` methods allow you to discard pending tasks to save processing power and memory.

### Canceling Specific or All Tasks
Use `Cancel(uint32_t taskId)` when a specific request is no longer needed. Use `CancelAll()` when the entire view or application context is destroyed.

```cpp
// Cancel a single task by ID
if (!myLoader.Cancel(taskId)) {
    // Task was not found or already completed
}

// Cancel all pending requests
myLoader.CancelAll();
```

## Best Practices and Performance

To maintain high performance in image-heavy applications, consider the following practices:

1.  **Request Throttling**: Avoid flooding the loader with hundreds of simultaneous requests. Use a queue or limit the number of active `Load()` calls based on screen visibility.
2.  **Memory Management**: Always specify `ImageDimensions` if the source image is significantly larger than the UI component, as this reduces the memory footprint of the decoded pixel data.
3.  **Lifecycle Awareness**: Always call `CancelAll()` when the parent object (e.g., a custom control or page) is removed from the stage to prevent callbacks from triggering on stale UI components.
4.  **Resource Re-use**: Reuse the `[AsyncImageLoader](./async-image-loader.md)` instance throughout the lifetime of a specific view rather than creating a new loader for every individual image.

→ See: [ImageLoader] (for synchronous loading scenarios)

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/async-image-loader)
