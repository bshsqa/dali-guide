---
id: texture-manager
title: "TextureManager"
sidebar_label: "TextureManager"
---
## Texture Manager Overview

The `TextureManager` acts as the central repository for shared GPU texture resources within the DALi engine, serving as the bridge between raw image buffers and the render graph. While `ImageLoader` handles the initial decoding of file formats, the `TextureManager` is responsible for registering these decoded textures into the engine's lifecycle, ensuring they are correctly tracked, cached, and available for use by visual elements.

You should use `TextureManager` whenever you need to manage the lifecycle of a `Texture` or `TextureSet` explicitly—particularly for dynamic content, shared assets across multiple UI components, or custom procedural textures generated at runtime. Unlike simple image loading, it provides the hook-based system required for high-performance memory management, making it distinct by keeping resource handles alive and ready for draw calls until explicitly removed.

→ See: [ImageLoader](image-loader-guide.md)

## Internal Lifecycle and Memory Management

The `TextureManager` employs an internal reference counting system to ensure that GPU resources remain valid while in use by the UI hierarchy. When a texture is registered, the manager takes ownership of the handle, preventing the engine from prematurely purging the asset from GPU memory during garbage collection phases.

> Note: The `TextureManager` does not automatically track the usage of a texture by individual actors. It is the developer's responsibility to invoke `RemoveTexture` when an asset is no longer required to prevent memory leaks and unnecessary GPU bloat.

## Thread Safety and Concurrency Model

Operations within the `TextureManager` are designed to be invoked from the main [rendering](./rendering.md) loop to ensure thread safety with the GPU command queue. While image decoding often occurs on background worker threads, the handoff to the `TextureManager` via `AddTexture` must be synchronized or performed on the main thread to prevent race conditions during the creation of internal GPU resource bindings.

## Texture Registration and Lifecycle API

The registration API provides a mechanism to map raw `Texture` or `TextureSet` objects to unique strings, which the engine uses for internal reference tracking.

### AddTexture (Texture)
This method registers a single `Texture` instance into the manager's cache, ensuring the resource is held by the engine for active use.

*   **WHAT:** Registers a `Texture` [object](./object.md) and associates it with a unique string handle.
*   **WHY:** Use this when you have a standalone texture generated via a custom renderer or a single-layer texture that needs to be referenced by multiple materials.
*   **HOW:**
    *   `texture`: A reference to the `Dali::Texture` [object](./object.md) to be managed.
    *   `preMultiplied`: A boolean indicating if the texture's RGB values are already pre-multiplied by the alpha channel.
    *   **Returns**: A `String` representing the unique URL handle for the registered resource.

```cpp
// Example: Registering a single texture
Dali::Texture myTexture = CreateCustomTexture(); 
bool isPreMultiplied = true;

std::string handle = Dali::Ui::TextureManager::AddTexture(myTexture, isPreMultiplied);
// The texture is now managed; 'handle' can be used for resource identification.
```

### AddTexture (TextureSet)
This method registers a `TextureSet` (a collection of textures), which is typically required for complex materials like multi-pass shaders or textures with separate alpha masks.

*   **WHAT:** Registers a `TextureSet` and assigns it a unique string identifier.
*   **WHY:** Use this for complex rendering needs where a shader requires multiple texture inputs (e.g., Diffuse + Normal + Specular maps).
*   **HOW:**
    *   `textureSet`: A reference to the `Dali::TextureSet` containing multiple textures.
    *   `preMultiplied`: A boolean indicating whether the input texture data is pre-multiplied.
    *   **Returns**: A `String` representing the unique URL handle.

```cpp
// Example: Registering a texture set
Dali::TextureSet myTextureSet = CreateMaterialSet();
std::string setHandle = Dali::Ui::TextureManager::AddTexture(myTextureSet, false);
```

### RemoveTexture
This method explicitly releases the manager's hold on the specified resource, allowing the engine to reclaim the associated GPU memory.

*   **WHAT:** Removes the texture resource associated with the provided URL handle.
*   **WHY:** Call this when the UI element is destroyed or when the image is no longer visible, preventing persistent memory growth.
*   **HOW:**
    *   `textureUrl`: The `String` handle previously returned by `AddTexture`.
    *   **Returns**: Returns the `TextureSet` associated with the removed handle, allowing for final cleanup or re-use.

```cpp
// Example: Reclaiming memory
Dali::TextureSet reclaimed = Dali::Ui::TextureManager::RemoveTexture(setHandle);
reclaimed.Reset(); // Properly dispose of the returned handle
```

## Integration with Image Loader Pipelines

The workflow involves using the `ImageLoader` to acquire a decoded buffer and passing that resource to the `[TextureManager](./texture-manager.md)` to finalize it for display. Once the `ImageLoader` signals completion, the resulting `Texture` or `TextureSet` should be passed to `AddTexture`. This pattern ensures that heavy decoding work happens off-main-thread, while the GPU registration remains atomic and predictable on the main thread.

## Performance Optimization and Best Practices

To avoid GPU stalls, avoid calling `AddTexture` and `RemoveTexture` during rapid frame updates. Instead, perform these operations during scene initialization or transition states. 

> Warning: Frequent registration and removal of large textures will lead to fragmentation of GPU memory and potential frame drops. Reuse existing textures whenever possible by caching their returned `String` handles instead of re-adding them.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/texture-manager)
