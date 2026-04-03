---
id: layer
title: "Layer"
sidebar_label: "Layer"
---
## Introduction to the [Layer](./layer.md) Component

The `Dali::Layer` is a specialized actor that serves as a container for spatial grouping and defines the [rendering](./rendering.md) order within a DALi scene graph. Unlike standard actors, which inherit their parent's coordinate space and draw order implicitly, a `Layer` introduces an explicit depth-controlled sorting mechanism that allows developers to manage complex UI stacks independently of the scene's tree structure.

You should use `Dali::Layer` when your application requires distinct visual planes (e.g., separating an HUD overlay from game content, or isolating background decorations from active interaction elements). It is the primary mechanism for controlling the vertical stacking (Z-order) of your application's visual interface. → See: [Actors]

## Internal Rendering Architecture and Z-Order

DALi renders the scene graph by traversing layers in ascending order based on their assigned depth value. By separating actors into different layers, the engine can batch [rendering](./rendering.md) commands more efficiently and ensure that elements on a higher-indexed layer consistently obscure those on a lower-indexed layer, regardless of their position in the scene graph tree.

The engine utilizes the depth buffer to handle occlusions, but `Layer` objects provide the initial sorting logic that dictates the [rendering](./rendering.md) pipeline order. When multiple layers are present, the engine renders them sequentially, resetting or respecting depth tests based on the layer's configured properties.

## [Layer](./layer.md) Lifecycle and Thread Safety

A `Dali::Layer` follows the standard [object](./object.md) lifecycle for DALi handles, where the [object](./object.md) remains alive as long as there is at least one handle pointing to it or it is attached to the stage. Creating a layer requires a call to the `Layer::New()` static method, which registers the layer with the internal scene graph manager.

> Warning: While `Layer` objects can be created on different threads, the modification of the scene graph (adding or removing layers/actors) must be performed from the DALi main event thread. Synchronization of properties between the main thread and the render thread is handled automatically by the engine's internal [update](./update.md)-to-render state transition.

```cpp
// Correct initialization of a Layer on the main thread
Dali::Layer myLayer = Dali::Layer::New();
myLayer.SetName("MainUIContainer");
Stage::GetCurrent().Add(myLayer);
```

## Configuring Layer Properties

Layers provide specific properties to control their behavior, such as clipping, sorting, and coordinate transformations. These properties define how the layer interacts with its children and the underlying rendering buffer.

### Managing Layer Attributes
You can manipulate layers using the `SetProperty` and `GetProperty` mechanisms. Key behaviors include:
- **Clipping**: Setting a layer to clip its children ensures that no visual content overflows the specified boundaries.
- **Sorting**: Adjusting the `[Layer](./layer.md)::Property::DEPTH` allows you to move the layer within the global Z-order.
- **Blending**: Layers can be configured to act as independent blending surfaces, useful for complex post-processing effects.

```cpp
// Example: Creating a clipping layer
Dali::Layer clipLayer = Dali::Layer::New();
clipLayer.SetProperty(Dali::Actor::Property::CLIPPING_MODE, Dali::ClippingMode::CLIP_CHILDREN);
Stage::GetCurrent().Add(clipLayer);
```

## Integration with Camera and Rendering Targets

While layers are rendered by default through the primary stage camera, they can be configured to interact with off-screen rendering targets or specific `CameraPlayer` setups. By linking a layer to a `CameraPlayer`, you can render distinct portions of your layer hierarchy into native images or different window targets.

### Using CameraPlayer with Layers
The `Dali::CameraPlayer` class allows you to direct rendering output to various targets, which can be coupled with specific layer hierarchies for advanced visual composition.

```cpp
// Example: Setting up a CameraPlayer to target a window
Dali::Window mainWindow = Window::GetCurrent();
Dali::CameraPlayer player = Dali::CameraPlayer::New();

// Set the target where the layer contents should be rendered
player.SetWindowRenderingTarget(mainWindow);

// Apply the camera configuration
Dali::DisplayArea area = {0, 0, 1920, 1080};
player.SetDisplayArea(area);
```

## Performance Implications and Optimization

Effective use of layers is critical for performance; grouping static actors into a single, non-changing layer can allow the engine to cache render state. Avoid creating too many layers, as each layer adds overhead to the scene graph traversal and potentially increases the number of draw calls by breaking batching opportunities.

> Note: For optimal performance, minimize the number of layers that utilize "Clipping" or "Blend" modes, as these trigger more expensive fragment operations and require additional depth buffer lookups. Organize your most static content in lower layers to ensure depth tests can perform early-Z rejection.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/layer)
