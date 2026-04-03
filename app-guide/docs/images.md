---
id: images
title: "images"
sidebar_label: "images"
---
## Introduction to Image Handling in DALi

The [images](./images.md) module in the DALi framework provides a structured approach for managing pixel data, ranging from raw buffer manipulation to integrating platform-specific image sources. It serves as the bridge between raw visual data and the displayable objects within your application's UI hierarchy.

Developers should utilize these APIs when they need to dynamically generate, load, or process pixel data that is not handled automatically by standard UI components. The module is distinct because it abstracts the complexity of pixel formats and hardware-level image interfaces, allowing developers to focus on the content of the image while maintaining high performance across various hardware architectures.

## Working with Pixel Formats

Pixel formats define the memory layout and channel structure of image data, which is essential for ensuring that pixel buffers are interpreted correctly by the display hardware. Configuring the correct format is a critical step before attempting to instantiate pixel data buffers.

> Note: While the Pixel API is the foundation, specific hardware may have restrictions on supported formats. Consult platform-level details regarding target device GPU capabilities if performance or [rendering](./rendering.md) artifacts occur.

## Managing Raw Pixel Data

The DALi framework uses specialized objects to wrap raw memory buffers. By encapsulating memory with its format, dimensions, and release protocols, the framework ensures that your application manages image data safely and efficiently within the graphics pipeline.

> Warning: Always ensure that the pixel buffer provided is of the size expected by the dimensions and pixel format specified. Providing an incorrect buffer size can lead to undefined behavior or memory access violations.

## Integrating Native Image Sources

Native image sources allow you to incorporate [images](./images.md) from platform-specific APIs or system-level buffers that exist outside the standard DALi image loading lifecycle. This is particularly useful when interfacing with camera feeds, system decoders, or other external hardware producers.

> Note: Integrating external sources is a platform-level detail. Please refer to your specific platform guide for implementation details regarding the `NativeImageInterface` class and its lifecycle management.

## Optimizing Text and Icons with Distance Fields

Distance fields allow for high-quality, resolution-independent [rendering](./rendering.md) of glyphs and vector-like shapes. By storing the distance to the nearest edge rather than raw pixels, this technique ensures that icons and [text](./text.md) remain crisp even when scaled aggressively.

> See: [TextRenderingModule] (Standard UI [text](./text.md) handling)

## Performance Best Practices for Images

Efficient image handling is paramount to maintaining a stable frame rate, especially when the UI requires frequent updates or large assets. Proper management of pixel buffers and memory allocation ensures your application remains responsive under load.

### Memory Lifecycle Management

Always minimize the lifetime of raw pixel data buffers. Once an image has been uploaded to the GPU, the source buffer should be released if it is no longer required for further CPU-side processing.

### Resource Loading Strategy

Avoid loading large [images](./images.md) on the main application thread. If your application handles a high volume of visual assets, implement an asynchronous loading pattern to ensure the UI remains fluid.

```cpp
#include <dali/dali.h>

// Example: Managing a hierarchy involving Actor interactions
// Note: This example demonstrates basic Actor lifecycle context.
// 'Images' are typically mapped to Actors via properties; 
// ensure your asset loading is handled prior to Actor attachment.

void CreateImageDisplay(Dali::Actor parent)
{
    // Actors are the primary object with which Dali applications interact.
    // They act as containers for visuals, including images.
    Dali::Actor imageContainer = Dali::Actor::New();
    
    // Set size to accommodate the target image dimensions
    imageContainer.SetResizePolicy(Dali::ResizePolicy::FIXED, Dali::Dimension::WIDTH);
    imageContainer.SetResizePolicy(Dali::ResizePolicy::FIXED, Dali::Dimension::HEIGHT);
    
    // Add the actor to the requested parent in the scene graph
    parent.Add(imageContainer);
    
    // Once added, the actor can be transformed or manipulated
    imageContainer.TranslateBy(Dali::Vector3(10.0f, 10.0f, 0.0f));
}
```

> Note: While `Dali::Actor` is the primary object for displaying UI elements, specific image-to-actor mapping relies on the use of `Dali::Property` maps defined at the platform level for resource loading. Always prioritize batch-adding children to prevent unnecessary scene graph rebuilds.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/images)
