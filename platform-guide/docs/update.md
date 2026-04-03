---
id: update
title: "update"
sidebar_label: "update"
---
## Introduction to the DALi Update Module

The DALi Update module is the core synchronization engine responsible for bridging the gap between application logic and the render pipeline. It provides a deterministic execution environment where the scene graph is updated, properties are calculated, and frame-specific transformations are applied before the hardware [rendering](./rendering.md) pass begins.

Developers should utilize the Update module when they require high-performance, frame-accurate modifications to scene objects that must bypass the standard event-queue latency. It is distinct from standard event-driven programming because it executes directly within the engine’s render-tick cycle, offering a controlled, low-overhead path for continuous [animation](./animation.md) or real-time simulation logic.

## Update Module Architecture and Lifecycle

The [update](./update.md) subsystem operates as a high-frequency loop integrated into the DALi engine's primary render sequence. This lifecycle ensures that every frame, the engine processes pending state changes—such as property baking or transform updates—before generating draw commands for the GPU.

The lifecycle is gated by the engine's frame processor. When an application registers a `FrameCallbackInterface`, it is invoked during the [update](./update.md) phase of the render tick. This tight coupling allows for minimal jitter in visual property updates, as the logic executes synchronously with the engine's internal state machine.

## Implementing Frame-Based Logic with FrameCallbackInterface

The `Dali::FrameCallbackInterface` is the primary mechanism for injecting custom C++ code into the DALi [update](./update.md) loop. By implementing this interface, you ensure your logic runs once per frame.

### Using FrameCallbackInterface::Update

The `Update` method is the entry point for your custom frame-based logic. It provides a `Dali::UpdateProxy` instance for interacting with actors and the `elapsedSeconds` since the last frame.

*   **WHAT:** Executes custom logic during the [update](./update.md) phase of a render frame.
*   **WHY:** Use this to perform real-time calculations that affect actor properties, such as procedural movement or custom animations.
*   **HOW:** Implement `Update(UpdateProxy &updateProxy, float elapsedSeconds)`. Return `true` to request continued updates, or `false` if the callback is no longer needed.
*   **CODE:**
```cpp
class MyUpdater : public Dali::FrameCallbackInterface {
public:
    bool Update(Dali::UpdateProxy &updateProxy, float elapsedSeconds) override {
        // Logic to update actor 123 position based on time
        Vector3 pos;
        if (updateProxy.GetPosition(123, pos)) {
            pos.y += 10.0f * elapsedSeconds;
            updateProxy.BakePosition(123, pos);
        }
        return true; // Keep calling this every frame
    }
};
```

> Note: The `Update` method must be non-blocking. Any heavy computation here will directly impact the frame rate and lead to dropped frames in the rendering pipeline.

## Managing Engine Updates via UpdateProxy

The `Dali::UpdateProxy` acts as a secure, restricted interface for modifying the scene graph from within the update thread. Because it runs during the update phase, it allows for direct property manipulation of actors using their unique IDs.

### Manipulating Actor State with UpdateProxy

The proxy exposes methods to query and modify actor properties, distinguishing between temporary per-frame settings and permanent "baked" values.

*   **WHAT:** Provides access to spatial and visual properties of actors within the update loop.
*   **WHY:** Necessary to perform direct transformations or property updates without the overhead of the main event loop.
*   **HOW:** Methods like `SetPosition` modify values for the current frame only, whereas `BakePosition` writes values to the scene graph permanently.
*   **CODE:**
```cpp
// Within a FrameCallbackInterface::Update implementation:
void UpdateActor(Dali::UpdateProxy& proxy, uint32_t actorId) {
    Vector3 pos;
    // Query current state
    if (proxy.GetPosition(actorId, pos)) {
        // Modify for this frame
        pos.x += 1.0f;
        proxy.BakePosition(actorId, pos);
    }
    
    // Manage ignored state
    proxy.SetIgnored(actorId, false);
}
```

> Warning: Always check the boolean return values of `UpdateProxy` methods. They return `false` if the specified actor ID is invalid or if the proxy lacks the necessary context to perform the operation.

## Threading Constraints and Synchronization

The Update module runs on a dedicated internal thread managed by the engine. Interactions via `UpdateProxy` are thread-safe by design because they are scoped to the update-tick lifecycle, but external communication with the main application thread requires care.

### Synchronization Mechanisms

When communicating between your main application logic and the update thread, use the sync-point mechanisms to ensure the engine and the application remain in lockstep.

*   **WHAT:** Uses `PopSyncPoint` and associated types to manage state synchronization.
*   **WHY:** Prevents race conditions when data is shared between asynchronous application threads and the synchronous update loop.
*   **HOW:** Call `PopSyncPoint` to retrieve a `NotifySyncPoint` value that represents the current state of the engine's update stack.

> Warning: Accessing scene graph objects directly from the `UpdateProxy` while the main thread is modifying the same objects can lead to undefined behavior; always rely on the `UpdateProxy` API to mediate access.

## Integration API Best Practices

To maintain performance, especially during high-frequency updates, it is crucial to minimize the workload within the `Update` callback.

*   **Optimization Pattern:** Cache actor IDs where possible. Querying properties for large numbers of actors should be done sparingly.
*   **Logic Separation:** Use `Bake` methods for permanent state changes and `Set` methods only for transient per-frame visual overrides.
*   **State Management:** Utilize `GetWorldTransformAndSize` if you require full spatial context, as it is more efficient than calling multiple individual `Get` methods for position, scale, and orientation.

→ See: [SceneGraph](https://developer.samsung.com/dali) (Architecture Overview)

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/update)
