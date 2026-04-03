---
id: frame-buffer
title: "FrameBuffer"
sidebar_label: "FrameBuffer"
---
## Introduction to the [FrameBuffer](./frame-buffer.md) Component

The `FrameBuffer` is a specialized [rendering](./rendering.md) resource that acts as an off-screen destination for graphics commands, allowing developers to redirect [rendering](./rendering.md) output to a texture rather than the immediate display surface. You should utilize the `FrameBuffer` when you need to perform multi-pass [rendering](./rendering.md), such as generating post-processing effects (blur, bloom, or color grading) or creating dynamic environment maps. It is distinct from standard surface [rendering](./rendering.md) because it decouples the drawing pipeline from the screen's refresh cycle, providing a programmable buffer for complex composition workflows.

→ See: [Rendering]

## [FrameBuffer](./frame-buffer.md) Lifecycle and Memory Management

`FrameBuffer` objects follow the standard DALi handle-based lifecycle, where the resource is automatically managed by the underlying engine based on reference counting. When a `FrameBuffer` is instantiated via `New()`, the engine allocates the necessary GPU memory and metadata, and the [object](./object.md) remains valid as long as at least one handle persists.

> Note: While DALi manages the lifecycle of the `FrameBuffer` handle, the associated `Texture` resources must be explicitly managed or attached to ensure they survive as long as the [rendering](./rendering.md) pass requires them.

```cpp
// Example: Creating and managing a FrameBuffer handle
{
    // Create a new FrameBuffer for a 1024x768 render pass
    Dali::FrameBuffer myFrameBuffer = Dali::FrameBuffer::New(1024, 768);
    
    // The handle points to the internal resource; reference count increases
    Dali::FrameBuffer secondaryHandle = myFrameBuffer;
} 
// myFrameBuffer and secondaryHandle go out of scope; 
// resource is flagged for cleanup if no other references exist.
```

## Attaching and Managing Color Textures

The primary function of a `[FrameBuffer](./frame-buffer.md)` is to act as a container for output attachments, specifically targeting `Texture` objects for color rendering. You must attach a valid texture to the `[FrameBuffer](./frame-buffer.md)` before issuing draw calls; otherwise, the rendering commands targeting this buffer will have no defined output.

### Attaching via AttachColorTexture
The `AttachColorTexture` method binds a created `Texture` instance to the buffer. You can use the base LOD attachment or specify exact mipmap levels and layers for advanced layered rendering.

```cpp
// Example: Attaching a texture to a FrameBuffer
Dali::Texture myTexture = Dali::Texture::New(Dali::TextureType::TEXTURE_2D, Dali::Pixel::RGBA8888, 1024, 768);
Dali::FrameBuffer myFrameBuffer = Dali::FrameBuffer::New(1024, 768);

// Attach the texture for color rendering
myFrameBuffer.AttachColorTexture(myTexture);

// Retrieve the texture later to use in a shader (e.g., as a sampler)
Dali::Texture output = myFrameBuffer.GetColorTexture();
```

## Threading and Synchronization Constraints

`[FrameBuffer](./frame-buffer.md)` operations are deeply integrated with the DALi render thread. While the handle-based API is accessible from the application thread, the actual allocation and attachment configuration are synchronized to ensure the render thread sees a consistent state.

> Warning: Avoid modifying or re-attaching textures while a `[FrameBuffer](./frame-buffer.md)` is actively being referenced in an active `RenderTask`. Such modifications should be orchestrated between frame updates to avoid undefined rendering results or race conditions.

## Working with FrameBuffer API Patterns

Developers interact with `[FrameBuffer](./frame-buffer.md)` primarily through the handle API, ensuring memory safety through reference counting and safe casting.

### Instantiation and Type Safety
Use `Dali::[FrameBuffer](./frame-buffer.md)::New` to allocate the resource. If you receive a `BaseHandle` (for instance, through a generic property system), always use `DownCast` to safely verify and retrieve the `[FrameBuffer](./frame-buffer.md)` interface.

```cpp
// Example: Safe instantiation and type checking
void SetupBuffer(Dali::BaseHandle handle)
{
    Dali::FrameBuffer fb = Dali::FrameBuffer::DownCast(handle);
    if (fb)
    {
        // Successful cast, safe to use fb
    }
}

// Create a FrameBuffer with specific internal attachments (e.g., Depth/Stencil)
Dali::FrameBuffer customBuffer = Dali::FrameBuffer::New(512, 512, Dali::FrameBuffer::Attachment::DEPTH);
```

## Best Practices for Advanced Rendering Pipelines

When designing complex pipelines involving multiple `FrameBuffers`, performance is heavily dictated by resource allocation and bandwidth usage. Frequent creation and destruction of `[FrameBuffer](./frame-buffer.md)` objects can lead to GPU stalls; instead, reuse existing buffers whenever possible during the application lifecycle.

* **Buffer Reuse:** Maintain a pool of buffers for common post-processing sizes.
* **Attachment Strategy:** Only attach the textures necessary for your pass. If you do not require depth information, do not include `Attachment::DEPTH` in your `New()` call to minimize memory footprint.
* **Pipeline Synchronization:** Always ensure that your `Texture` sampling matches the `[FrameBuffer](./frame-buffer.md)` dimensions to avoid sampling artifacts or hardware-level errors.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/frame-buffer)
