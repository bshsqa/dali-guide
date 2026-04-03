---
id: update
title: "update"
sidebar_label: "update"
---
## Introduction to the Update Framework

The Update Framework in DALi provides a high-performance mechanism for performing frame-by-frame synchronization of application state. It allows developers to execute custom logic that is tightly coupled with the render loop, ensuring that UI properties and data states are updated precisely before the render pass occurs.

You should use the Update Framework when your application requires low-latency, real-time data binding or complex procedural animations that must remain perfectly synchronized with the display refresh rate. Unlike standard signal-based event handling, the Update Framework provides a dedicated pipeline for state modification, making it distinct for its efficiency in handling rapid, continuous changes that would otherwise incur overhead on the main event thread.

## Understanding the Update Proxy

The `UpdateProxy` serves as the primary interface for managing and scheduling updates within the application lifecycle. By interacting with the proxy, developers ensure that property modifications and state calculations are batched effectively, preventing redundant layout or render calculations.

### Utilizing the Update Proxy

The `UpdateProxy` provides a gateway to hook into the [update](./update.md) cycle, allowing for property synchronization that occurs before the frame is processed by the GPU.

*   **What:** The `UpdateProxy` manages the lifecycle of registered [update](./update.md) tasks and frame callbacks.
*   **Why:** Use this to coordinate multi-[object](./object.md) state changes that must be applied simultaneously to avoid partial-frame renders.
*   **How:** Acquire the proxy instance from the application context to register your frame-dependent tasks.
    *   `RegisterCallback`: Adds a custom interface to the system.
    *   `UnregisterCallback`: Removes a previously added interface to halt the [update](./update.md) cycle for that [object](./object.md).

> Note: The `UpdateProxy` is designed for high-frequency updates; ensure that logic performed within the proxy remains lightweight to avoid impacting the frame budget.

## Implementing Frame Callbacks

Implementing the `FrameCallbackInterface` allows developers to define a specific block of logic that the DALi system executes automatically before each render pass. This is the cornerstone of frame-synchronized application logic.

### The FrameCallbackInterface

This interface defines a single pure virtual method that acts as the entry point for your per-frame logic.

*   **What:** This interface provides the signature for the `Update` method which is called during the application's [update](./update.md) phase.
*   **Why:** Implementing this interface allows you to encapsulate state-[update](./update.md) logic in a reusable class that conforms to the DALi [update](./update.md) cycle.
*   **How:** Override the `Update(float elapsedSeconds)` method.
    *   `elapsedSeconds` (float): The duration in seconds since the last frame [update](./update.md), useful for time-based [animation](./animation.md) calculations.

```cpp
#include <dali/public-api/dali-core.h>

class MyUpdateTask : public Dali::FrameCallbackInterface
{
public:
    void Update(float elapsedSeconds) override
    {
        // Custom logic to modify actor properties based on time
        float rotation = mRotationValue + (elapsedSeconds * 45.0f);
        mActor.SetOrientation(Degree(rotation));
    }
    
    Dali::Actor mActor;
    float mRotationValue = 0.0f;
};
```

## Registering and Managing Update Tasks

Once a `FrameCallbackInterface` is implemented, it must be registered with the update system to become active. This ensures the engine knows to invoke your logic during the render loop.

### Registration Process

Registration involves passing your interface implementation to the update management system, typically handled via the application or context object.

*   **What:** This connects your custom `FrameCallbackInterface` object to the internal update loop.
*   **Why:** An unregistered callback will never be invoked; registration enables the hook.
*   **How:** Use the registration method associated with the update provider.
    *   `AddCallback(FrameCallbackInterface& callback)`: Registers the interface to receive frames.
    *   `RemoveCallback(FrameCallbackInterface& callback)`: Stops receiving frame updates.

```cpp
// Usage example for registering a task
MyUpdateTask myTask;
myTask.mActor = someActor;

// Registering the task to the update loop
Dali::UpdateProxy::Get().AddCallback(myTask);

// ... later, ensure clean up
Dali::UpdateProxy::Get().RemoveCallback(myTask);
```

## Best Practices for Real-Time Updates

Maintaining consistent performance while using the update API requires strict adherence to frame budget constraints. Because the update method runs every frame, inefficiencies here will immediately cause dropped frames.

*   **Avoid Allocations:** Never perform memory allocations (e.g., `new`, `std::vector::push_back` causing reallocations) inside the `Update` method.
*   **Minimize Logic:** Keep the instruction count inside the `Update` method to a minimum; offload heavy computations to worker threads or compute shaders where possible.
*   **Property Batching:** If updating multiple actor properties, use the `Update` loop to batch these changes, as they are applied to the scene graph in a single coherent state.

> Warning: Performing blocking I/O or long-running synchronous operations within a frame callback will cause the application to stutter or hang. Always keep the code execution time well below the 16.6ms threshold required for 60FPS.

→ See: [SceneGraphAPI] for details on how property modifications affect the scene graph structure.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/update)
