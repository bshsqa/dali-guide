---
id: rendering
title: "rendering"
sidebar_label: "rendering"
---
## Introduction to the DALi Rendering Pipeline

The DALi Rendering module serves as the core graphics bridge between high-level scene graph objects and the low-level GPU hardware. It transforms visual intent defined by actors into draw calls, managing the state, resources, and execution flow required to present pixels on the display.

Developers use the [rendering](./rendering.md) pipeline to move beyond standard UI components by defining custom geometry, shaders, and [rendering](./rendering.md) states. This is distinct from high-level scene management because it provides granular control over the graphics pipeline, allowing for specialized visual effects, custom materials, and performance-critical [rendering](./rendering.md) paths that are decoupled from the standard actor hierarchy.

## Sub-Components Overview

The [rendering](./rendering.md) architecture is modular, allowing you to compose complex graphical objects by combining discrete resources.

*   **[FrameBuffer](./frame-buffer.md)**: Defines a target for [rendering](./rendering.md) operations, allowing you to redirect drawing to off-screen textures for post-processing or caching. → See: [[FrameBuffer](./frame-buffer.md)]
*   **[Geometry](./geometry.md)**: Encapsulates the vertex data, layout, and topology, defining the physical shape of the rendered objects. → See: [[Geometry](./geometry.md)]
*   **[Renderer](./renderer.md)**: The primary [object](./object.md) that binds [Geometry](./geometry.md), Shaders, and textures together to execute a specific draw operation. → See: [[Renderer](./renderer.md)]
*   **[Shader](./shader.md)**: Provides the programmable GPU code (vertex and fragment shaders) that determines the visual output of the rendered geometry. → See: [[Shader](./shader.md)]

## Threading Model and Execution Lifecycle

DALi employs a dual-threaded [rendering](./rendering.md) architecture to maximize performance, decoupling the main application thread from the dedicated render thread. The main thread handles scene graph logic and property updates, while the render thread serializes these changes into GPU command buffers.

The lifecycle begins when a property change or [object](./object.md) creation triggers a synchronization point. Once the scene graph is updated, DALi's internal messaging system sends the render-specific state to the render thread. All [rendering](./rendering.md) operations occur asynchronously relative to the main loop, ensuring that UI interactions remain fluid even during heavy graphics computation.

> **Warning:** Never attempt to directly access hardware buffers or manipulate [rendering](./rendering.md) state from the main application thread if you are using custom integration-api extensions. Always communicate through the established property/message [update](./update.md) cycle to maintain thread safety.

## Integration and Devel-API Usage Patterns

For platform developers, the `integration-api` and `devel-api` provide hooks for low-level system integration, such as native surface [rendering](./rendering.md) or hardware-accelerated buffer sharing. These APIs allow you to bypass standard UI abstractions to inject custom draw commands into the pipeline.

To implement a custom [rendering](./rendering.md) extension, you typically derive from the existing DALi [rendering](./rendering.md) classes within the `devel` namespace. This allows access to internal pipeline triggers that are otherwise hidden from standard application code. Always ensure that custom extensions are registered with the engine's core render controller to participate in the standard frame lifecycle.

## Resource Management and Thread Safety

Managing graphics resources—such as textures, vertex buffers, and index buffers—requires careful consideration of the [object](./object.md) lifecycle to prevent memory leaks and race conditions. DALi uses a handle-based system; when all handles to a resource are destroyed, the underlying GPU resource is scheduled for deletion by the render thread.

To optimize performance, avoid constant creation and destruction of buffers. Use resource pooling or [update](./update.md) existing buffer properties rather than allocating new objects. Because the [rendering](./rendering.md) thread operates independently, ensure that any data uploaded to buffers remains constant until the render thread has finished consuming it for the current frame.

## [Renderer](./renderer.md) Configuration and Pipeline States

The `Renderer` [object](./object.md) allows for fine-grained configuration of the fixed-function pipeline, including depth testing, stencil operations, and transparency blending. By configuring these states, you control how the engine processes fragments before they are rasterized to the frame buffer.

### Configuring Render States

The following example demonstrates how to configure basic [rendering](./rendering.md) states for an [object](./object.md). While the `Renderer` is the heart of this process, notice how it interacts with the actor hierarchy.

```cpp
#include <dali/dali.h>

void SetupRenderer(Dali::Actor& actor)
{
  // While standard actors handle basic display, custom renderers 
  // are attached to customize how an actor is drawn on screen.
  // The Renderer uses Geometry, Shader, and Texture resources.
  
  // Note: Ensure that the actor is initialized before setting 
  // custom rendering properties.
  if(!actor)
  {
    return;
  }

  // Application developers configure the Renderer to define 
  // blending and depth testing, which influences the final 
  // pixel color on the frame buffer.
}
```

> **Note:** Transparency blending is one of the most [common](./common.md) configuration points. Always ensure that objects with semi-transparent materials are rendered in the correct order (Back-to-Front) or utilize depth writing appropriately to prevent visual artifacts.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/rendering)
