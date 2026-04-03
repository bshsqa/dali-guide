---
id: geometry
title: "Geometry"
sidebar_label: "Geometry"
---
## [Geometry](./geometry.md) Overview

Dali::[Geometry](./geometry.md) is the fundamental container for geometric data in the DALi [rendering](./rendering.md) pipeline, acting as a bridge between raw vertex/index data and the GPU's rasterization stage. Unlike high-level UI controls that encapsulate geometry, `Dali::Geometry` provides granular control over primitive topology and multi-stream vertex data.

You should use `Dali::Geometry` when you need to define custom shapes—such as meshes, point clouds, or complex parametric surfaces—that require specific vertex buffer configurations or custom indexing strategies. It is distinct from high-level visual components because it is a low-level [rendering](./rendering.md) primitive that must be paired with a `Renderer` to be displayed on screen.

## Core Architecture and Handle Lifecycle

`Dali::Geometry` utilizes DALi’s internal handle-based memory management, where the [object](./object.md) lifecycle is governed by the engine’s reference counting system. When you create a `Dali::Geometry` [object](./object.md), it serves as a lightweight handle to an internal implementation that resides within the [rendering](./rendering.md) graph.

To create a new instance, use the static `New()` method. Because it follows handle semantics, copying or assigning a `Geometry` handle simply increases the internal reference count, pointing both handles to the same underlying GPU resources.

```cpp
// Creating and managing a Geometry handle
Dali::Geometry geometry = Dali::Geometry::New();

// Handles can be copied; both point to the same underlying resource
Dali::Geometry copy = geometry;

// Downcasting from a generic BaseHandle is supported for integration
Dali::BaseHandle base = geometry;
Dali::Geometry casted = Dali::Geometry::DownCast(base);
```

## Configuring Primitive Topology

The primitive type determines how the GPU interprets the vertex stream. By setting the `Dali::[Geometry](./geometry.md)::Type`, you define whether your buffers represent independent points, lines, or triangles.

Use `SetType()` to define the primitive interpretation. Failing to specify this will result in default behavior that may not match your vertex ordering.

```cpp
Dali::Geometry geometry = Dali::Geometry::New();

// Configure the geometry to render as triangles
geometry.SetType(Dali::Geometry::TRIANGLES);

// Verify current configuration
Dali::Geometry::Type type = geometry.GetType();
```

> Warning: Always ensure the `[Geometry](./geometry.md)::Type` matches the winding order and connectivity expectations of your associated shader program to avoid undefined rendering behavior.

## Managing Vertex Buffers

`Dali::[Geometry](./geometry.md)` supports a multi-stream architecture, allowing you to attach multiple `VertexBuffer` objects to a single geometry primitive. This is essential for interleaving different vertex attributes (e.g., positions in one buffer, texture coordinates in another) to optimize memory bandwidth and cache usage.

Use `AddVertexBuffer` to register a buffer. The method returns a `std::size_t` index, which serves as a handle for the buffer within that specific geometry object.

```cpp
// Assuming vertexBuffer is a pre-configured Dali::VertexBuffer
Dali::Geometry geometry = Dali::Geometry::New();
std::size_t bufferIndex = geometry.AddVertexBuffer(vertexBuffer);

// Querying buffer count
std::size_t count = geometry.GetNumberOfVertexBuffers();

// Removing a buffer when no longer needed
geometry.RemoveVertexBuffer(bufferIndex);
```

> Note: While `Dali::[Geometry](./geometry.md)` manages the attachment of buffers, the `VertexBuffer` itself holds the actual data. Ensure the `VertexBuffer` lifespan is maintained as long as the `[Geometry](./geometry.md)` requires it. 
> → See: [VertexBuffer](https://docs.tizen.org/application/native/api/wearable/latest/group__dali__core__rendering.html#ga73a465922e964319c7270b240167f1b2)

## Indexing and Primitive Drawing

Indexing allows you to reuse shared vertices, significantly reducing memory usage for models with dense geometry. `Dali::[Geometry](./geometry.md)` supports both 16-bit and 32-bit index buffers, enabling flexibility based on the complexity of your mesh.

Call `SetIndexBuffer` to associate index data. If no index buffer is provided, the GPU renders the geometry using the sequential order of the vertex buffers.

```cpp
// Example: Using 16-bit indices for a small mesh
uint16_t indices[] = {0, 1, 2, 2, 3, 0};
geometry.SetIndexBuffer(indices, 6);

// Example: Using 32-bit indices for larger models
uint32_t largeIndices[] = {0, 1, 2, 1000, 1001, 1002};
geometry.SetIndexBuffer(largeIndices, 6);

// Unsetting the index buffer to return to non-indexed rendering
geometry.SetIndexBuffer(nullptr, 0);
```

## Threading and Thread Safety

`Dali::[Geometry](./geometry.md)` handles are thread-safe in terms of reference counting (creation, destruction, and copy). However, modification of the underlying vertex or index data buffers is subject to the thread-safety rules of the rendering architecture.

Data updates should be performed during the application's update phase or via custom message passing to the rendering thread to prevent race conditions while the GPU is actively reading from the buffers. Avoid modifying a `[Geometry](./geometry.md)` object's buffers while a `[Renderer](./renderer.md)` associated with that geometry is currently executing a draw call in the pipeline.

## Best Practices for Geometry Integration

To achieve optimal rendering performance, minimize the frequency of buffer updates. Rebuilding `[Geometry](./geometry.md)` objects (adding/removing buffers or changing types) triggers state changes in the graphics driver, which can introduce latency.

- **Prefer Static Geometry**: Upload vertex data once and reuse it across multiple frames.
- **Buffer Packing**: Interleave data into fewer vertex buffers if the shader input permits, reducing the number of overhead streams for the GPU to manage.
- **Cleanup**: Always explicitly unset buffers or nullify handles when they are no longer required to free up GPU-side memory via the reference counting system.

```cpp
// Efficient workflow:
void SetupGeometry(Dali::Geometry& geo, Dali::VertexBuffer& vb) {
    geo.SetType(Dali::Geometry::TRIANGLES);
    geo.AddVertexBuffer(vb);
    // Bind to a Renderer (not shown here)
    // → See: [Renderer]
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/geometry)
