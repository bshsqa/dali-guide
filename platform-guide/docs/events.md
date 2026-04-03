---
id: events
title: "events"
sidebar_label: "events"
---
## Introduction to the DALi Event System

The DALi event system is the foundational pipeline responsible for translating raw platform-level input into actionable application [signals](./signals.md). It manages the lifecycle of input data from hardware abstraction through the scene graph, ensuring that user interactions are routed correctly to the intended UI components.

Developers should utilize the event module whenever they need to process touch, key, or gesture-based input within a DALi application. What makes this system distinct is its seamless integration with the render-loop, providing a thread-safe boundary between low-level platform [events](./events.md) and high-level scene graph updates.

## Event Architecture and Threading Model

DALi employs a decoupled architecture where platform [events](./events.md) are queued and processed by the `Dali::Integration::Core` component. This ensures that heavy input processing does not block the platform's native event loop, maintaining UI responsiveness during complex interactions.

### The Integration Core
The `Dali::Integration::Core` class serves as the bridge between platform-specific input and the engine's internal [update](./update.md) cycle. It acts as the primary orchestrator for event ingestion and synchronization.

**Method: `QueueEvent`**
*   **What:** Injects an event into the processing queue to be handled in the next [update](./update.md) cycle.
*   **Why:** Use this when porting a new platform or injecting custom hardware input that must be synchronized with the DALi frame [update](./update.md).
*   **How:** Takes a `const Event&` as its only parameter. The event is stored internally and processed during the `ProcessEvents()` call.
*   **Code:**
    ```cpp
    void InjectCustomEvent(Dali::Integration::Core& core, const Dali::Integration::Event& event) {
        // Queue the event for the next processing cycle
        core.QueueEvent(event);
    }
    ```

**Method: `ProcessEvents`**
*   **What:** Triggers the engine to consume all queued events and route them through the hit-testing and signal-delivery systems.
*   **Why:** This must be called within the render thread before the `Update` phase to ensure the state of the scene graph is current for that frame.
*   **How:** No parameters. This method executes the logic that transforms queued raw data into high-level application signals.
*   **Code:**
    ```cpp
    void TickEngine(Dali::Integration::Core& core) {
        // Flush the queue and update event states
        core.ProcessEvents();
        // Proceed to update the scene graph
    }
    ```

> Warning: Calling `ProcessEvents` outside of the dedicated render thread may result in race conditions. Always ensure synchronization with the `Dali::Integration::Core` lifecycle.

## Sub-Components Overview

The event family is modularized to support different interaction paradigms, ranging from simple pointer clicks to complex multi-touch sequences.

*   **Gesture Detection:** Provides high-level recognition logic for taps, pans, pinches, and rotations by analyzing sequential event data. → See: [GestureDetection]
*   **Gesture Types:** Defines the data structures and state constants used to describe physical interactions detected by the engine. → See: [GestureTypes]
*   **Input Events:** Contains the low-level representation of touch, key, and hover events before they are processed into gestures. → See: [InputEvents]

## Hit Testing and Dispatching

Hit testing is the process by which DALi identifies which scene graph nodes are targets for a specific input event. The engine performs this resolution by traversing the scene graph and checking against the spatial boundaries of each actor.

The hit-test algorithm ensures that events are delivered only to relevant actors, respecting z-order and clipping. By default, this happens automatically when `ProcessEvents` is invoked; however, for complex layouts, developers should ensure that actor `Sensitive` and `Enabled` properties are configured correctly to receive these hits.

## Integration API Lifecycle

For platform developers, managing the `Core` lifecycle is critical for stable event handling during application startup, suspension, or context loss.

### Managing Core Lifecycle
The `Dali::Integration::Core` provides specific hooks to notify the engine of significant system events, such as context loss or restoration.

**Method: `NotifyContextLost` / `NotifyContextRegained`**
*   **What:** Signals to the engine that the underlying graphics context has become invalid or has been restored.
*   **Why:** These are essential during platform porting to prevent rendering attempts or event dispatching when the GPU state is not available.
*   **How:** Access these via the `ContextNotifierInterface` retrieved from the `Core` instance.
*   **Code:**
    ```cpp
    void HandleContextChange(Dali::Integration::Core& core, bool lost) {
        auto* notifier = core.GetContextNotifier();
        if (lost) {
            notifier->NotifyContextLost();
        } else {
            notifier->NotifyContextRegained();
        }
    }
    ```

## Best Practices for Event Handling

To maintain high performance and responsiveness:

1.  **Event Throttling:** Ensure that input generation on the platform side does not exceed the display's refresh rate significantly, as unnecessary `QueueEvent` calls will increase CPU usage without improving precision.
2.  **Hit-Test Optimization:** Keep the scene graph as shallow as possible. Large hierarchies require more computation during the hit-test phase, which can introduce latency in event delivery.
3.  **Thread Safety:** Always inject events via `QueueEvent` from your platform layer; do not attempt to interact with DALi actors directly from the platform thread while the `Core::Update` phase is in progress.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/events)
