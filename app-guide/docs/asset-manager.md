---
id: asset-manager
title: "asset-manager"
sidebar_label: "asset-manager"
---
## Introduction to AssetManager

The `AssetManager` serves as the centralized orchestration layer for handling resource retrieval, caching, and lifecycle management within a DALi application. It acts as the primary interface for resolving both local filesystem resources and remote network assets, abstracting the complexities of asynchronous loading and memory optimization away from the UI implementation.

Developers should utilize the `AssetManager` whenever the application requires dynamic content loading, such as [images](./images.md), media files, or configuration data. It is distinct in its ability to manage global caching policies and provide unified state notification, ensuring that visual components—such as those found in → See: [[AnimatedImageView](./animated-image-view.md)]—receive validated and ready-to-render resources without blocking the main application thread.

## Initializing the AssetManager

Proper initialization of the `AssetManager` ensures that resource policies and cache limits are established before the application begins [rendering](./rendering.md) its primary view hierarchy. The manager should be instantiated during the application startup phase to provide global access for all subsequent UI components.

> Note: While the `AssetManager` provides global resource management, its configuration must occur at the platform-level detail. Please refer to the platform guide for specific details on persistent configuration storage.

## Loading and Retrieving Assets

The `AssetManager` provides methods to fetch resources asynchronously, ensuring that the application remains responsive while data is being prepared. By leveraging internal caching, the manager minimizes redundant network requests or filesystem I/O, significantly improving overall application performance.

## Monitoring Asset States

Tracking the lifecycle of an asset request is critical for providing feedback to the user, such as displaying a loading spinner or placeholder image. The `AssetManager` utilizes signal-based communication to notify observers when an asset has transitioned from a requested state to a loaded or failed state.

## Managing Asset Cache

The cache management functionality allows developers to exert control over the application's memory footprint by clearing stale assets or pre-loading frequently used items. Effective cache maintenance is essential for high-performance applications that handle a high volume of dynamic assets over long sessions.

## Handling Asset Errors

Even with robust network connectivity, developers must implement error handling to manage scenarios such as missing files, invalid URLs, or request timeouts. The `AssetManager` reports these conditions through specific error codes and signal payloads, allowing for graceful degradation of the UI.

> Warning: Always implement a fallback mechanism when an asset load fails; leaving an `AnimatedImageView` or other view with a null resource can lead to unexpected visual artifacts.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/asset-manager)
