---
id: texture-manager
title: "TextureManager"
sidebar_label: "TextureManager"
---
## Introduction to [TextureManager](./texture-manager.md)

The `TextureManager` is a centralized [utility](./utility.md) within the DALi framework designed to manage the lifecycle, caching, and resource sharing of GPU textures. Unlike basic image loading methods that may create duplicate texture objects, `TextureManager` provides a unified interface to load, track, and share texture resources across multiple UI components, significantly optimizing GPU memory usage.

You should use `TextureManager` when your application displays the same image resource in multiple locations or when you require fine-grained control over the asynchronous loading and lifecycle of textures. By leveraging the manager, you avoid redundant memory allocations and benefit from built-in caching mechanisms that persist textures as long as at least one component references them.

## Adding Textures to the Cache

The `AddTexture` functionality allows you to request an image resource and receive a `TextureSet` or associated handle that can be applied to your UI components. The manager automatically checks the internal cache before initiating a new load request, ensuring that identical requests return existing, shared resources.

### Adding an Image Resource

To add a texture, pass the file path and necessary loading parameters to the manager. This operation is asynchronous; the manager will process the request and notify your application when the texture is ready for use.

> Note: If the requested texture is already in the cache, the manager returns a handle to the existing instance immediately, incrementing the reference count for the resource.

```cpp
// Example: Adding a texture for use in an ImageActor
auto& textureManager = Dali::Toolkit::TextureManager::Get();
Dali::Toolkit::VisualUrl url("my_image.png");

// Requesting the texture
auto textureId = textureManager.AddTexture(url, Dali::Toolkit::ImageDimensions(100, 100));

// Use the textureId to construct your visuals or actors
```

## Removing and Releasing Resources

Resource management is critical for maintaining a low memory footprint in complex UI scenes. `RemoveTexture` allows you to explicitly signal that a specific texture instance is no longer needed by a component.

### Decrementing Reference Counts

When you call `RemoveTexture`, the manager decrements the reference count for that resource. If the count reaches zero, the manager proceeds to release the GPU memory associated with that texture.

```cpp
// Example: Removing a texture no longer in use
auto& textureManager = Dali::Toolkit::TextureManager::Get();

// Once the actor is detached or destroyed, notify the manager
textureManager.RemoveTexture(textureId);
```

> Warning: Failure to call `RemoveTexture` for dynamic resources will result in memory leaks, as the cache will retain the texture indefinitely, assuming it may be required again in the future.

## Handling Texture State and Notifications

Texture loading occurs asynchronously to ensure the UI thread remains responsive. The `[TextureManager](./texture-manager.md)` provides mechanisms to track these state transitions, allowing developers to react to loading completion or failure.

### Responding to Loading Signals

You can connect to signals provided by the manager to receive updates on the status of your texture requests. This is particularly useful for showing loading spinners or fallback graphics when an image takes time to load.

```cpp
// Example: Connecting to loading notifications
Dali::Toolkit::TextureManager& manager = Dali::Toolkit::TextureManager::Get();

manager.TextureLoadedSignal().Connect([](const Dali::Toolkit::TextureId& id) {
    // Texture is now ready to be rendered
});
```

## Optimizing Memory with Texture Sharing

Texture sharing is the primary performance benefit of using `[TextureManager](./texture-manager.md)`. By centralizing texture requests, the framework ensures that even if ten different `ImageActor` instances use the same asset, only one instance resides in GPU memory.

### Best Practices for Sharing
- **Use Consistent Dimensions:** When requesting the same asset from multiple locations, ensure the `ImageDimensions` parameters are identical to trigger cache hits.
- **Reference Management:** Always ensure that your components release their references promptly to allow the manager to purge stale textures.

## Troubleshooting Common Loading Scenarios

When working with `[TextureManager](./texture-manager.md)`, you may encounter issues related to file access or invalid image formats. 

- **Invalid File Paths:** Ensure your `VisualUrl` points to a reachable location. If the file is not found, the manager will typically return a failure notification through its signal system.
- **Format Mismatches:** If an image format is unsupported, the `[TextureManager](./texture-manager.md)` will fail to process the load. Always verify your asset formats are compatible with DALi's supported image types.
- **Cache Contention:** If you are loading a large volume of textures simultaneously, the cache may reach its capacity. The manager will handle this internally, but for very memory-constrained environments, ensure you are removing textures as soon as they are no longer visible.

→ See: [ImageLoader]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/texture-manager)
