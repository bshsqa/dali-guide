---
id: input-events
title: "Input Events"
sidebar_label: "Input Events"
---
## Introduction to Input-Events

Input-[events](./events.md) in DALi represent the bridge between raw platform-level hardware interrupts and the semantic interaction layer of the GUI framework. Unlike high-level gestures, which aggregate input data over time, input-[events](./events.md) provide granular access to discrete hardware states, such as specific key codes, mouse wheel ticks, or individual touch points.

You should use input-[events](./events.md) when your application requires low-level control over interaction hardware, such as implementing custom gesture recognizers, managing keyboard-driven navigation, or handling precision pointer devices. These [events](./events.md) are distinct from other event variants because they bypass high-level interpretation logic, offering the lowest possible latency between platform input and application-side reaction.

## Input Event Dispatch Lifecycle

The input event dispatch cycle describes how data transitions from the kernel to the engine's scene graph. When the platform provides input, the event is queued in the `Adaptor` layer and processed during the next [update](./update.md) cycle of the main loop.

1. **Hardware Capture:** The platform abstraction layer captures the raw interrupt (e.g., Linux `evdev` or Tizen input).
2. **Queue Injection:** Events are pushed into the DALi Event Queue within the `Adaptor` thread.
3. **Synchronization:** At the beginning of the `Update` phase, the engine flushes the queue into the `Core` thread.
4. **Hit-Testing:** The engine performs a reverse-z-order traversal of the scene graph to determine the target `Actor`.
5. **Signal Emission:** The event is dispatched to the hit actor's input [signals](./signals.md); if unconsumed, it propagates to parent actors based on the event's capture/bubble rules.

## Key Input Event Classes

These classes represent the primary data structures injected into the application when hardware changes occur.

### TouchPoint
A `TouchPoint` represents a single point of contact on a digitizer, containing information about its state, pressure, and coordinate mapping.

*   **WHAT:** Stores the state and coordinate data of a specific contact point during a touch sequence.
*   **WHY:** Use this to track multi-touch interactions where the relative movement or pressure of individual fingers matters.
*   **HOW:** Access properties such as `state` (Down, Motion, Leave, Up, Interrupted) and `screenPosition` (Vector2).

```cpp
void OnTouch(Actor actor, const TouchEvent& event) {
  for(std::size_t i = 0; i < event.GetPointCount(); ++i) {
    const TouchPoint& point = event.GetPoint(i);
    if(point.state == PointState::DOWN) {
      // Handle initial contact
    }
  }
}
```

### KeyEvent
The `KeyEvent` class encapsulates physical key presses, containing the key name, key code, and modifier states (Shift, Alt, Ctrl).

*   **WHAT:** Provides metadata about hardware key events.
*   **WHY:** Essential for implementing focus-based navigation or keyboard shortcuts.
*   **HOW:** Use `GetKeyName()` to identify the physical key and `GetState()` to distinguish between press and release.

> Warning: Always check the `keyModifier` bitmask to ensure combinations like `Ctrl+C` are handled correctly; otherwise, you may trigger events on single key presses.

### WheelEvent
The `WheelEvent` describes the movement of a pointer device's scroll wheel.

*   **WHAT:** Encapsulates the rotation direction, amount, and the coordinate of the cursor at the time of the event.
*   **WHY:** Used for custom scrollable containers or zoom functionality.
*   **HOW:** The `z` value in `delta` indicates the scroll amount; positive values usually represent upward/forward movement.

## Thread Safety and Synchronization

Input events are strictly delivered on the **Main (UI) Thread**. Because DALi's scene graph is not thread-safe for mutation, you must never modify the scene graph or trigger expensive calculations directly inside an event callback.

> Note: If you need to perform heavy processing, use `Dali::Task` or a custom background thread and return to the main thread via the `EventThreadCallback` or `IdleCallback` mechanisms to update the UI state.

## Integration API for Platform Porting

When porting DALi to new hardware, the `Integration` namespace provides the necessary hooks to inject raw events into the engine.

*   **`Integration::EventBuffer`**: The primary buffer used to submit events to the core.
*   **`AddEvent()`**: A method used to enqueue platform events so the core can process them during the next tick.

```cpp
// Example of injecting a simulated key event via the Integration API
void InjectKey(Integration::Core& core, const std::string& keyName) {
  KeyEvent event(keyName, "key", 0, 0, 0, KeyEvent::DOWN);
  core.AddEvent(event);
}
```

## Signal Connectivity and Event Filtering

Connecting to input signals follows the standard DALi signal/slot pattern, but requires careful attention to the return value of the callback to manage event propagation.

*   **Return `true`:** Consumes the event. The event will stop propagating through the scene graph.
*   **Return `false`:** The event continues to propagate to parent actors or the default platform handlers.

```cpp
// Example of connecting and consuming a touch signal
myActor.TouchedSignal().Connect([](Actor actor, const TouchEvent& event) -> bool {
  // Logic to handle touch
  
  // Return true to indicate we have handled the event and it should not propagate
  return true; 
});
```

→ See: [Event Propagation System] (Context: Parent document)

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/input-events)
