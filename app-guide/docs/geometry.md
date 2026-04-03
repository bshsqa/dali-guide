---
id: geometry
title: "Geometry"
sidebar_label: "Geometry"
---
## Introduction to [Geometry](./geometry.md)

[Geometry](./geometry.md) is the core component in DALi for defining the spatial structure of a visual element. By explicitly managing vertex and index buffers, [Geometry](./geometry.md) provides the low-level data necessary for the GPU to render custom meshes, such as complex 3D shapes or specialized primitive [layouts](./layouts.md) that standard DALi controls cannot represent.

You should use the [Geometry](./geometry.md) component when you need full control over the mesh topology, such as when importing vertex arrays or implementing custom procedural shapes. It is distinct from higher-level controls because it serves as the raw data container that must be associated with a `Renderer` to be visible on the screen.

## Creating and Configuring [Geometry](./geometry.md) Objects

The [Geometry](./geometry.md) [object](./object.md) is a handle to the GPU-bound data structures that define your mesh. Before you can render anything, you must instantiate this [object](./object.md) and define its primitive type to inform the graphics pipeline how to interpret your vertex data.

### Instantiating [Geometry](./geometry.md)
To create a new geometry [object](./object.md), use the static `New()` method. This initializes the internal handle required to store vertex and index data.

```cpp
#include <dali/public-api/rendering/geometry.h>

// Create a new Geometry instance
Dali::Geometry myGeometry = Dali::Geometry::New();
```

### Defining the Primitive Type
The primitive type determines whether your vertices are treated as isolated points, connected line segments, or triangles. Use `SetType()` to define this behavior.

```cpp
// Set the geometry to render as triangles
myGeometry.SetType(Dali::Geometry::TRIANGLES);

// Retrieve the current type
Dali::Geometry::Type type = myGeometry.GetType();
```

> Note: Changing the geometry type after buffers have been populated may result in visual artifacts if the data layout does not match the expected primitive topology.

## Managing Vertex Buffers

Vertex buffers hold the primary attribute data—such as positions, texture coordinates, and normals—that define your geometry. DALi allows you to associate multiple vertex buffers with a single Geometry object, enabling flexible data layouts.

### Adding and Removing Buffers
You can add a `VertexBuffer` using `AddVertexBuffer()`. This method returns the index of the newly added buffer, which you can use for future reference.

```cpp
// Assuming 'myVertexBuffer' is a valid Dali::VertexBuffer instance
std::size_t bufferIndex = myGeometry.AddVertexBuffer(myVertexBuffer);

// Query the count of attached buffers
std::size_t count = myGeometry.GetNumberOfVertexBuffers();

// Remove a buffer by its index
myGeometry.RemoveVertexBuffer(bufferIndex);
```

> Warning: Always ensure that the `VertexBuffer` contains data compatible with the current shader program's input attributes to avoid rendering failures.

## Defining Index Buffers

Index buffers are used to optimize mesh rendering by allowing the GPU to reuse vertex data. Instead of duplicating vertices for shared corners, you define a sequence of indices that point to the vertices in your vertex buffer.

### Configuring Index Data
DALi supports both 16-bit and 32-bit indices. Use `SetIndexBuffer()` to provide the raw array and the count of indices.

```cpp
// Example: Using 16-bit indices for a small mesh
uint16_t indices[] = { 0, 1, 2, 2, 3, 0 };
myGeometry.SetIndexBuffer(indices, 6);

// To unset the index buffer, pass a null pointer and count 0
myGeometry.SetIndexBuffer(nullptr, 0);
```

> Note: Using 32-bit indices is necessary for meshes that exceed 65,535 vertices. Ensure you choose the correct method signature based on the complexity of your geometry.

## Handling Geometry Types

The `Dali::[Geometry](./geometry.md)::Type` enumeration dictates how the GPU draws your vertex array. Selecting the correct type is critical for performance and correct visual representation.

*   **POINTS**: Renders each vertex as an individual point.
*   **LINES**: Renders pairs of vertices as distinct line segments.
*   **TRIANGLES**: Renders triplets of vertices as filled triangles.

Use `SetType()` during the initialization phase to ensure the geometry is configured before it is attached to a `[Renderer](./renderer.md)`.

→ See: [Renderer](https://tizen.org) (The component responsible for linking Geometry and Shaders).

## Best Practices for Geometry Resource Usage

To maximize rendering performance, manage your Geometry lifecycle carefully. Avoid recreating geometry objects every frame; instead, update the underlying `VertexBuffer` data if the shape needs to change dynamically.

*   **Batching**: Minimize the number of Geometry objects by combining multiple meshes into a single vertex buffer where possible.
*   **Buffer Cleanup**: Always unset index buffers (`nullptr`, 0) if you transition from an indexed rendering mode to a non-indexed mode to prevent reading stale memory.
*   **Handle Usage**: Since `[Geometry](./geometry.md)` is a handle class, pass it by value or store it in your actor's controller class to ensure it remains in memory as long as the renderer requires it.

```cpp
// Example of a minimal setup sequence
Dali::Geometry geometry = Dali::Geometry::New();
geometry.SetType(Dali::Geometry::TRIANGLES);
geometry.AddVertexBuffer(myVertexBuffer);
geometry.SetIndexBuffer(myIndices, indexCount);

// The 'geometry' handle is now ready to be passed to a Dali::Renderer
```

> Platform-level detail: Advanced memory management, such as mapping GPU buffers directly for frequent data updates, involves platform-level details regarding buffer usage flags and memory mapping. Refer to the platform-specific DALi guide for performance-sensitive use cases.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/geometry)
