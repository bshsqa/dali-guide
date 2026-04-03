---
id: asset-manager
title: "asset-manager"
sidebar_label: "asset-manager"
---
## Introduction to the Asset Manager

The Asset Manager is a core architectural component within the DALi engine responsible for the lifecycle, retrieval, and caching of visual and binary assets. It serves as the primary interface for bridging the gap between raw file system data and the engine's [rendering](./rendering.md) resources.

By centralizing resource acquisition, the Asset Manager ensures that duplicate assets are deduplicated in memory and provides a consistent asynchronous pathway for loading, which is critical for maintaining high frame rates in complex GUI applications. It is distinct from standard file-loading utilities because it integrates directly with the DALi [rendering](./rendering.md) pipeline and garbage collection systems.

## Asset Manager Architecture and Internal Flow

The Asset Manager operates as a singleton-patterned service that manages a queue of load requests. Internally, `asset-manager.cpp` implements a producer-consumer model where requests are pushed from the main thread (or application logic) and processed by background worker threads to prevent UI blocking.

Memory ownership is managed through reference-counted handles. When an asset is requested, the manager checks its internal cache; if found, it returns an existing reference; otherwise, it triggers a load operation. Once the data is loaded and uploaded to the GPU, the Asset Manager notifies the requesting [object](./object.md), ensuring that resources are kept alive only as long as they have active references.

## Devel-API Integration and Usage

The `asset-manager.h` surface exposes the `Devel` namespace functionality, allowing engine developers to interact with the loading lifecycle. Usage typically involves submitting a request [object](./object.md) and binding a callback for the completion event.

### Requesting Assets

To fetch an asset, developers utilize the [asset-manager](./asset-manager.md)'s registered loading protocols. This ensures that assets are processed through the internal dependency resolution system.

```cpp
// Example: Basic Asset Request
void LoadEngineAsset(const std::string& path) {
  auto& assetManager = Dali::Integration::GetAssetManager();
  
  // Create an asset request handle
  auto request = Dali::AssetRequest::New(path);
  
  // Register a callback for completion
  request.FinishedSignal().Connect([](Dali::AssetResult result) {
    if (result.Success()) {
      // Use the resource
    }
  });
  
  assetManager.Submit(request);
}
```

## Lifecycle Management and Thread Safety

The Asset Manager is thread-safe regarding request submission, but asset post-processing and signal emission typically occur on the main thread to ensure compatibility with other UI components.

> Note: All DALi `Handle` types associated with assets must be managed within the engine's main loop. Never attempt to manipulate the scene graph from a background thread during an asset-loading completion signal.

When an asset is no longer required, the Asset Manager automatically handles cleanup if the reference count drops to zero. Developers should avoid manual memory management for assets loaded through this system.

## Asynchronous Asset Loading Strategies

Asynchronous loading is the default behavior. By utilizing the `Devel-API` signal infrastructure, developers can non-blockingly retrieve resources and update their UI state only once the data is ready for the GPU.

### Implementing Non-Blocking Retrieval

To maintain a fluid user experience, always prefer asynchronous requests over synchronous loads. This prevents the "hitch" associated with disk I/O and shader compilation.

```cpp
// Implementing a non-blocking load for a custom UI component
void MyComponent::LoadTextureAsync(const std::string& url) {
  Dali::AssetRequest request = Dali::AssetRequest::New(url);
  
  // Use a lambda to handle the completion
  request.FinishedSignal().Connect([this](Dali::AssetResult result) {
    if(result.Success()) {
      this->SetTexture(result.GetResource());
    }
  });
  
  Dali::Integration::GetAssetManager().Submit(request);
}
```

## Error Handling and Resource Recovery

The Asset Manager provides detailed error reporting via the `AssetResult` object. Common failure modes include file-not-found, unsupported formats, or GPU memory exhaustion.

> Warning: Always check `result.Success()` before attempting to access the underlying resource. Accessing a failed request will throw a DALi exception.

In the event of a failure, the engine can be configured to provide "fallback" assets (e.g., a magenta checkerboard texture) to allow the application to continue running without visual artifacts.

## Performance Tuning and Memory Footprint

The Asset Manager maintains an internal cache to optimize repeated asset requests. Developers can tune the cache size or trigger an explicit flush if the application is nearing memory constraints.

### Managing Memory Thresholds

Developers can monitor the Asset Manager's footprint and perform manual clears during scene transitions to release unused memory back to the system.

```cpp
void ClearCachedAssets() {
  auto& assetManager = Dali::Integration::GetAssetManager();
  
  // Flush assets that currently have zero active references
  assetManager.ClearCache(Dali::AssetCachePolicy::Unreferenced);
}
```

→ See: [Dali::Ui::[AbsoluteLayout](./absolute-layout.md)] (used for managing visual placement after assets are loaded).

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/asset-manager)
