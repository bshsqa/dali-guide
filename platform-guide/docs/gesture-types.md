---
id: gesture-types
title: "Gesture Types"
sidebar_label: "Gesture Types"
---
## Overview of [Gesture Types](./gesture-types.md)

`gesture-types` provides the foundational enumeration and data structures used to classify raw touch sequences into high-level interaction primitives such as taps, pans, pinches, and long presses. Developers should use these types when implementing custom gesture detectors or processing gesture notifications to maintain consistent semantic handling across the DALi scene graph. Unlike raw touch [events](./events.md), which report individual point changes, `gesture-types` encapsulate the state and intent of the entire interaction, making them distinct and preferable for UI navigation and control manipulation.

## Gesture State Lifecycle

The gesture lifecycle defines the progression of a touch interaction, informing the application of the specific phase of a recognized gesture. Understanding these states is critical for managing UI feedback and state-dependent logic.

### State Transitions
Every gesture proceeds through a defined set of states represented by the `GestureState` enumeration. These states allow the framework to distinguish between the beginning of a user interaction, ongoing updates, and finalization.

*   **Started**: The gesture detector has successfully identified the start of a recognized pattern.
*   **Continued**: The gesture is in progress, and new coordinate data or parameters (e.g., scale or rotation) are available.
*   **Finished**: The user has lifted their fingers, completing the gesture successfully.
*   **Cancelled**: The system or application has interrupted the gesture (e.g., due to a scene change or application focus loss).

> **Note:** Always verify the `GestureState` inside your signal callback to prevent logic execution during a cancelled or partial gesture.

## Gesture Data Structures

Gesture data structures hold the contextual information generated during the recognition process, providing developers with spatial and temporal details required to [update](./update.md) the UI.

### Common Gesture Properties
Regardless of the specific gesture type (e.g., `PanGesture`, `PinchGesture`), all gestures share core coordinate data that relates the interaction to the DALi coordinate system.

*   **Screen Coordinates**: The absolute position of the gesture interaction on the physical display.
*   **Local Coordinates**: The position of the interaction relative to the `Actor` that received the event, calculated by transforming screen coordinates into the actor's local space.

### Implementation Example: PanGesture
The `PanGesture` structure provides the velocity and displacement of the panning motion, allowing for smooth UI scrolling or [object](./object.md) translation.

```cpp
// Example: Extracting data from a PanGesture
void OnPanGesture(Dali::Actor actor, const Dali::PanGesture& gesture)
{
  if (gesture.state == Dali::GestureState::Continuing)
  {
    // Retrieve displacement since the last update
    Vector2 displacement = gesture.displacement;
    // Apply transformation to the actor
    actor.TranslateBy(Vector3(displacement.x, displacement.y, 0.0f));
  }
}
```

## Thread Safety and Data Synchronization

DALi processes raw input on a dedicated Input thread and dispatches gesture events to the Main (Update) thread. This architectural separation ensures that heavy processing within your gesture callbacks does not drop input events, but it requires strict adherence to thread-safety rules.

### Callback Constraints
All gesture signals are triggered on the Main thread. Developers must never attempt to modify the scene graph or update UI properties from any other thread context. Because gesture data is passed by const reference, it is safe to read these values, but they must not be stored beyond the scope of the callback as the memory management of the underlying gesture object is handled by the DALi event pipeline.

## Integration with Input Events

The integration layer bridges the gap between the `GestureDetector` instances and the `Actor` hierarchy, ensuring that signals are routed correctly to the intended UI components.

### Connecting Detectors
To handle gestures, you create a detector instance, configure its sensitivity (if applicable), and attach it to an `Actor`.

```cpp
// Setup a TapGestureDetector
Dali::TapGestureDetector tapDetector = Dali::TapGestureDetector::New();
tapDetector.Attach(myActor);

// Connect the signal
tapDetector.DetectedSignal().Connect(&OnTapDetected);

// Signal handler implementation
void OnTapDetected(Dali::Actor actor, const Dali::TapGesture& gesture)
{
  if (gesture.state == Dali::GestureState::Finished)
  {
    // Execute logic for a successful tap
  }
}
```
→ See: [Events Parent Page] for details on general event propagation.

## Performance Considerations for Gesture Handling

Processing high-frequency gesture data—especially for continuous gestures like `Pinch` or `Pan`—requires efficient handling to maintain a smooth 60/120 FPS frame rate.

### Optimization Strategies
- **Minimize Allocation**: Do not instantiate new objects or perform complex heap allocations inside gesture callbacks. 
- **Deferred Processing**: If a gesture update requires expensive logic, flag the state in the callback and perform the computation in the next `OnUpdate` or `OnStageConnection` cycle.
- **Detector Management**: Only attach detectors to actors that actually require gesture input; having thousands of active `GestureDetector` objects can introduce overhead in the event dispatching phase.

> **Warning:** Excessive logging or heavy I/O operations inside `DetectedSignal` callbacks will directly increase input latency, causing "input lag" that is perceptible to the end user.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/gesture-types)
