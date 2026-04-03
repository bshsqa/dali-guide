---
id: utility
title: "utility"
sidebar_label: "utility"
---
## Introduction to the NPatch Utility Module

The NPatch Utility module provides a specialized suite of helper functions within the DALi framework designed to automate the parsing, geometry generation, and [rendering](./rendering.md) preparation of 9-patch [images](./images.md). By abstracting the complexities of grid-based image slicing and stretch-region calculation, it enables developers to create resolution-independent UI elements that maintain aspect ratio integrity across diverse screen densities.

You should utilize this module whenever you need to implement scalable assets where specific border regions must remain fixed while central areas stretch. Unlike standard image [rendering](./rendering.md), these utilities ensure that decorative UI components like buttons, speech bubbles, and panels deform correctly without visual distortion.

## Internal Architecture and Integration

This module is partitioned into `NPatchUtility`, which focuses on data analysis and pixel buffer inspection, and `NPatchHelper`, which manages the interface between the CPU-calculated geometry and the GPU-side `Renderer`. Together, they bridge raw image data and DALi's [rendering](./rendering.md) pipeline.

The `NPatchUtility` namespace acts as the data-parsing layer, determining if an asset requires NPatch processing and calculating the required stretch indices from buffer data. The `NPatchHelper` then consumes these indices to generate efficient `Geometry` and configure the necessary shader uniforms.

→ See: [Visuals](Visuals-Module)

## Lifecycle and Memory Management

Lifecycle management in the NPatch module is primarily focused on the immutable nature of the `Geometry` objects produced by `NPatchHelper` and the transient state of the `PixelBuffer` during parsing. Developers are responsible for the ownership of the `Renderer` and the `Internal::NPatchData` objects passed to helper methods.

> Warning: `NPatchHelper` methods that generate `Geometry` return objects that must be managed by the scene graph or component lifecycle. Failure to reference these objects correctly can result in resource deallocation while the renderer still expects the buffer to exist.

## Thread Safety and Concurrency Models

All methods within `NPatchUtility` and `NPatchHelper` are designed to be invoked on the main application thread, as they frequently interact with DALi [rendering](./rendering.md) resources. While `NPatchUtility::ParseBorders` can be executed on a worker thread when processing `PixelBuffer` data for image loading, the resulting `NPatchUtility::StretchRanges` must be safely marshaled to the main thread before invoking `NPatchHelper` methods.

> Note: Accessing `Renderer` or `Geometry` instances from background threads is strictly prohibited, as these types are not thread-safe and must only be modified by the main DALi event loop.

## Devel-API Usage Patterns

The `Devel-API` allows direct access to the `ParseBorders` mechanism, enabling custom image loading logic to intercept NPatch metadata during the initial load phase. This is the primary entry point for integrating custom image decoders with the DALi NPatch [rendering](./rendering.md) system.

### Parsing and [Geometry](./geometry.md) Generation
The `ParseBorders` method reads the edge pixels of a `PixelBuffer` to determine how the image should be sliced.

```cpp
// Example: Parsing an image and creating the corresponding grid geometry
Dali::Devel::PixelBuffer buffer = LoadPixelBuffer("my_button.9.png");
Dali::Ui::NPatchUtility::StretchRanges stretchX, stretchY;

if(Dali::Ui::NPatchUtility::ParseBorders(buffer, stretchX, stretchY)) {
    // Successfully identified stretch regions
    Dali::Uint16Pair gridSize(3, 3); // Standard 3x3 for a 9-patch
    Dali::Geometry geometry = Dali::Ui::NPatchHelper::CreateGridGeometry(gridSize);
    
    // Geometry can now be attached to a Renderer
}
```

### Applying Uniforms
Once geometry is prepared, `ApplyTextureAndUniforms` maps the texture to the renderer using the internal NPatch data structure.

```cpp
void SetupRenderer(Dali::Renderer& renderer, const Dali::Internal::NPatchData* data) {
    // Applies texture and configures uniform offsets for the shader
    Dali::Ui::NPatchHelper::ApplyTextureAndUniforms(renderer, data);
}
```

## Performance Optimization for NPatch Processing

To maximize performance when handling multiple NPatch textures, prefer `CreateBorderGeometry` over `CreateGridGeometry` if the center of your image does not require rendering, effectively reducing the number of vertices processed by the GPU. 

> Note: When dynamically updating NPatch properties, minimize the number of calls to `RegisterStretchProperties` per frame, as each call triggers a uniform update that necessitates re-validation of the shader state.

### Efficient Geometry Creation
Use `CreateBorderGeometry` to minimize draw calls by only generating vertices for the border frame.

```cpp
// Create a specialized geometry for a 5x4 grid layout
Dali::Uint16Pair gridSize(5, 4);
Dali::Geometry borderOnly = Dali::Ui::NPatchHelper::CreateBorderGeometry(gridSize);

// Registering stretch ranges for the custom geometry
Dali::Ui::NPatchUtility::StretchRanges stretch; // Assume populated
Dali::Ui::NPatchHelper::RegisterStretchProperties(myRenderer, "uStretchRanges", stretch, 1024);
```

### Utility Helper Methods
Use `IsNinePatchUrl` during asset loading to perform early filtering of files, preventing unnecessary parsing attempts on standard image formats.

```cpp
void LoadImage(const std::string& url) {
    if(Dali::Ui::NPatchUtility::IsNinePatchUrl(url)) {
        // Proceed with NPatch processing path
    } else {
        // Proceed with standard image path
    }
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/utility)
