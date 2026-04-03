---
id: rendering
title: "rendering"
sidebar_label: "rendering"
---
# Introduction to DALi Rendering

Rendering in DALi represents the low-level graphics pipeline interface that allows developers to move beyond standard UI components. By leveraging custom geometries, shaders, and frame buffers, it enables the creation of high-performance, platform-agnostic graphics tailored to specific visual requirements.

Rendering is distinct because it provides direct control over the graphics hardware abstraction layer within the DALi scene graph. You should use this feature when the built-in visual system cannot achieve your desired graphical effect, such as custom procedural geometry, specialized post-processing shaders, or complex off-screen [rendering](./rendering.md) passes.

# Rendering Sub-Components Overview

The [rendering](./rendering.md) module is composed of several specialized components that work in concert to process graphics data and output it to the screen. Understanding these components is essential for constructing custom graphics pipelines.

- **[FrameBuffer](./frame-buffer.md)**: Manages off-screen [rendering](./rendering.md) targets, allowing the results of drawing commands to be stored in textures for later use. → See: [[FrameBuffer](./frame-buffer.md)]
- **[Geometry](./geometry.md)**: Defines the mesh data, including vertex attributes and index arrays, which form the skeletal structure of rendered objects. → See: [[Geometry](./geometry.md)]
- **[Renderer](./renderer.md)**: Orchestrates the draw call by binding [Geometry](./geometry.md), Shaders, and Textures to define how a specific [object](./object.md) appears on screen. → See: [[Renderer](./renderer.md)]
- **[Shader](./shader.md)**: Contains the programs that run on the GPU to determine vertex positioning and pixel color. → See: [[Shader](./shader.md)]

# Configuring Render States

Render states define how the GPU processes fragments during the draw process, covering aspects such as depth testing, blending modes, and face culling. Configuring these states allows you to achieve transparency effects, optimize performance by discarding hidden surfaces, and define front-facing geometry.

> Note: Incorrect render state configuration, particularly regarding depth testing, is a primary cause of [rendering](./rendering.md) artifacts in complex 3D scenes. Always ensure your blending mode matches your texture alpha requirements.

# Managing Textures and Samplers

Textures provide the surface detail for rendered geometry, while samplers define how those textures are filtered and addressed during sampling in the shader. Properly managing these resources is critical for visual quality and memory efficiency in your [rendering](./rendering.md) passes.

# Working with Visual Renderers

The `DecoratedVisualRenderer` allows developers to wrap or enhance standard DALi [visuals](./visuals.md) with custom [rendering](./rendering.md) properties or effects. This component acts as a bridge between the high-level actor-based UI system and the low-level [rendering](./rendering.md) pipeline.

# Performance Best Practices

Efficient [rendering](./rendering.md) requires careful management of GPU resources to minimize driver overhead and prevent frame drops. The following guidelines should be observed:

- **Buffer Updates**: Prefer static geometry when possible. If dynamic updates are required, use persistent buffers to minimize memory transfers between the CPU and GPU.
- **UniformBlocks**: Group related uniform data into blocks to reduce the number of individual uniform set calls, which improves CPU performance during the render pass preparation phase.
- **Resource Re-use**: Share Shaders and Textures across different Renderers to reduce state changes in the GPU pipeline.

> Warning: Frequent creation and destruction of [Renderer](./renderer.md) objects will cause significant performance degradation. Always pool your [rendering](./rendering.md) resources whenever the application lifecycle allows.

```cpp
// Example: Basic Actor initialization and hierarchy manipulation
// This demonstrates how objects are integrated into the DALi scene graph
#include <dali/dali.h>

void SetupScene()
{
  Dali::Actor root = Dali::Actor::New();
  
  // Create a child actor to represent a renderable object
  Dali::Actor child = Dali::Actor::New();
  
  // Apply transformations
  child.TranslateBy(Dali::Vector3(10.0f, 0.0f, 0.0f));
  child.RotateBy(Dali::Degree(45.0f), Dali::Vector3::ZAXIS);
  
  // Add to the scene graph
  root.Add(child);
  
  // Basic hierarchy check
  if(root.GetChildCount() > 0)
  {
    Dali::Actor found = root.FindChildById(child.GetProperty<uint32_t>(Dali::Actor::Property::ID));
  }
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/rendering)
