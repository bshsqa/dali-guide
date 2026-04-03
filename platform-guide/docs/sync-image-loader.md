---
id: sync-image-loader
title: "SyncImageLoader"
sidebar_label: "SyncImageLoader"
---
## Overview of [SyncImageLoader](./sync-image-loader.md)

The `SyncImageLoader` serves as a specialized mechanism within the DALi framework for scenarios where image decoding must be deterministic and tightly coupled to the execution flow. Unlike its asynchronous counterpart, this loader executes image processing tasks inline, blocking the calling thread until the pixel data is ready or an error occurs.

Developers should utilize `SyncImageLoader` when an application logic requires immediate access to a `PixelBuffer` before proceeding to the next stage of visual composition. It is particularly useful for pre-caching assets during a controlled loading state, where the performance trade-off of a blocking call is acceptable in exchange for guaranteed synchronous data availability.

→ See: [[AsyncImageLoader](./async-image-loader.md)]

## Component Lifecycle and Threading Model

The `SyncImageLoader` follows the standard DALi handle-based lifecycle, requiring explicit instantiation through its factory methods. As a handle [object](./object.md), it manages an underlying implementation pointer, allowing for efficient copying and move semantics.

### Initialization and Lifetime
The `New()` static method is the primary entry point for creating an instance. The [object](./object.md) remains valid as long as at least one handle persists, and its lifecycle is tied to the scope of these handles.

```cpp
#include <dali/dali.h>
#include <dali/devel-api/adaptor-framework/image-loader.h>

void InitializeLoader() {
  // Creating a new instance using the static factory method
  Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::New();
  
  // The loader is now ready for use.
}
```

> Warning: Because `[SyncImageLoader](./sync-image-loader.md)` operates synchronously, calling its methods on the main UI thread will cause the rendering loop to hang until the image is processed. Always ensure that synchronous operations are performed on a dedicated background worker thread to prevent frame drops or unresponsive UI states.

## Synchronous Loading API

The loading interface provides multiple overloads to handle varying requirements for image resolution, sampling, and orientation correction. Each `Load` method triggers the decoding process immediately upon invocation.

### Configuring Load Parameters
The `Load` methods return a `uint32_t` representing a unique task ID, which can be used to track or cancel the operation. 

*   `url`: The file path or URI of the source image.
*   `dimensions`: A `Dali::ImageDimensions` object specifying the target width and height.
*   `fittingMode`: Determines how the image is scaled to fit the requested dimensions.
*   `samplingMode`: Defines the algorithm (e.g., box, linear) used to downsample the image.
*   `orientationCorrection`: A boolean flag that, when true, automatically applies metadata-based rotation (e.g., EXIF orientation).

```cpp
// Example of a specific loading configuration
uint32_t taskId = loader.Load(
    "path/to/image.jpg", 
    Dali::ImageDimensions(512, 512), 
    Dali::FittingMode::SCALE_TO_FIT, 
    Dali::SamplingMode::BOX_THEN_LINEAR, 
    true
);
```

## Resource Management and Cancellation

Even in a synchronous flow, there are instances where a pending or ongoing operation needs to be aborted due to rapid state changes in the application, such as a user navigating away from a view.

### Using Cancel and CancelAll
The `Cancel()` method attempts to stop a specific task if it resides in the queue, while `CancelAll()` provides a clean slate for the loader instance.

```cpp
// Canceling a specific task by its ID
if (!loader.Cancel(taskId)) {
    // Task may have already completed or failed
}

// Clearing all pending operations
loader.CancelAll();
```

> Note: Cancellation is only effective if the task has not yet transitioned to the post-processing phase. Once the image buffer is generated and the completion signal is triggered, cancellation cannot revert the allocation.

## Signal Handling and Completion Callbacks

The `ImageLoadedSignal()` provides the primary mechanism for receiving the final output after the synchronous operation finishes. 

### Connecting to SignalType
By connecting a callback to `ImageLoadedSignal()`, the application receives the `PixelBuffer` and the original task ID. This ensures that the caller can correlate the loaded data with the original request.

```cpp
void OnImageLoaded(uint32_t taskId, Dali::PixelBuffer pixelBuffer) {
    // Process the loaded image data
    if (pixelBuffer) {
        // Successfully decoded image
    }
}

// Signal connection
loader.ImageLoadedSignal().Connect(OnImageLoaded);
```

## Best Practices for Integration

To ensure robust performance within the DALi engine, developers must adhere to memory management and thread-safety guidelines.

### Integration Patterns
*   **Memory Ownership:** The `PixelBuffer` returned via the signal holds the decoded bitmap data. Ensure this buffer is transferred to a `Texture` or `PixelData` object promptly to allow the internal cache to reclaim memory.
*   **Downcasting:** When receiving generic `BaseHandle` objects, use the provided `DownCast` method to safely retrieve the `[SyncImageLoader](./sync-image-loader.md)` interface.
*   **Avoid Main-Thread Blocking:** Never call `Load` with large high-resolution files on the main thread; this will result in a visual freeze of the application.

```cpp
// Safely downcasting a handle
Dali::BaseHandle handle = GetLoaderHandle();
Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::DownCast(handle);

if (loader) {
    // Proceed with safe operation
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/sync-image-loader)
