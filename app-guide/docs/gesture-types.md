---
id: gesture-types
title: "Gesture Types"
sidebar_label: "Gesture Types"
---
## Introduction to [Gesture Types](./gesture-types.md)

The `gesture-types` module provides a high-level abstraction layer over raw touch inputs, allowing developers to recognize complex user interaction patterns such as tapping, panning, pinching, and rotating. You should use these gesture types instead of raw touch [events](./events.md) when you need to interpret multi-touch sequences or require state-based feedback (e.g., tracking the progress of a pan) without manually calculating velocity or displacement vectors.

Unlike raw touch [events](./events.md), which report discrete state changes (Down, Motion, Up), gesture types represent semantic interactions with an `Actor`. By attaching a `GestureDetector` to an actor, DALi handles the mathematical heavy lifting of recognizing the gesture, enabling you to focus on the application logic rather than input arithmetic.

## Understanding Gesture Properties

Gesture properties provide the essential metadata required to react to user input, capturing the spatial and temporal context of an interaction. These properties are [common](./common.md) across the gesture suite and ensure that your application can accurately track the origin and evolution of a touch interaction.

### Core Gesture Data
Every gesture [object](./object.md) provides a `time` property (in milliseconds) indicating when the event occurred and the `state` of the gesture (e.g., `Started`, `Continuing`, `Finished`, or `Cancelled`). The `GetState()` method is used to determine if a gesture is currently in progress, which is vital for continuous gestures like panning where you must [update](./update.md) UI state repeatedly.

> Note: While touch points are provided, gesture types are distinct from touch [events](./events.md); gesture processing occurs after the touch stream is analyzed for specific patterns. → See: `Touch` [events](./events.md).

## Working with Tap and Pan Gestures

Tap and Pan gestures represent the most [common](./common.md) interactions in mobile applications: discrete actions and continuous movement. 

### Tap Gesture
The `TapGesture` detects single or multiple touch contact points followed by a release within a specific area. It is typically used for button activations or item selections.

```cpp
// Example: Detecting a tap on an actor
void OnTap(Actor actor, const TapGesture& gesture)
{
  if (gesture.state == GestureState::FINISHED)
  {
    // Handle tap interaction
  }
}

// Setup
auto tapDetector = TapGestureDetector::New();
tapDetector.Attach(myActor);
tapDetector.DetectedSignal().Connect(&OnTap);
```

### Pan Gesture
The `PanGesture` is used for dragging objects or scrolling lists. It provides velocity and displacement data, allowing you to create smooth, physics-based interactions.

```cpp
void OnPan(Actor actor, const PanGesture& gesture)
{
  if (gesture.state == GestureState::CONTINUING)
  {
    // Use gesture.displacement to update position
    actor.SetPosition(actor.GetCurrentPosition() + gesture.displacement);
  }
}
```

## Implementing Pinch and Rotation Gestures

Pinch and Rotation gestures allow users to interact with the geometry of a scene directly through intuitive multi-touch movements.

### Pinch Gesture
The `PinchGesture` provides a `scale` factor, which represents the ratio of the distance between the two touch points relative to their initial distance. You can use this value to scale an actor's size dynamically during the gesture.

### Rotation Gesture
The `RotationGesture` provides a `rotation` property in radians. This value allows you to rotate an actor relative to its current orientation, which is essential for image editing or map interfaces.

```cpp
void OnPinch(Actor actor, const PinchGesture& gesture)
{
  if (gesture.state == GestureState::CONTINUING)
  {
    float newScale = actor.GetProperty(Actor::Property::SCALE).Get<Vector3>().x * gesture.scale;
    actor.SetScale(newScale);
  }
}
```

## Configuring Gesture Detection

Configuring your gesture detectors is crucial for balancing responsiveness with accidental input filtering. Every detector provides parameters to tune how strictly a gesture is recognized.

### Thresholds and Sensitivity
For a `PanGestureDetector`, you can specify the minimum number of touch points required (e.g., single-finger pan vs. two-finger pan) and minimum distance thresholds. This prevents jittery inputs from triggering unintentional movements.

*   `SetRequiresTouchPoints(uint)`: Defines how many fingers must be active to trigger the gesture.
*   `SetMinimumDistance(float)`: Sets the distance threshold in pixels that the user must drag before the pan is recognized, helping to ignore small, unintentional movements.

> Warning: Setting thresholds too high can make your application feel unresponsive, while setting them too low may cause "ghost" gestures. Always test on the target display density.

## Handling Gesture Signals

Gesture detection in DALi is signal-driven, meaning you connect your logic to specific signals emitted by the `GestureDetector` objects. 

### Connection Patterns
When you connect to a detector's signal, the callback function receives the actor being acted upon and the specific gesture object. It is important to check the `state` of the gesture within your callback to ensure that your logic handles the start, progression, and completion of the gesture appropriately.

*   **Signal Propagation:** Gesture events are consumed by the highest-level actor in the scene graph that has a detector attached. If an actor does not consume the gesture, it may propagate depending on the scene graph structure.
*   **Performance:** Avoid heavy computations within gesture callbacks, as these occur on the main event loop and can affect frame rates during high-frequency events like panning or pinching.

```cpp
// Example: Attaching multiple detectors
auto pinchDetector = PinchGestureDetector::New();
pinchDetector.Attach(myActor);
pinchDetector.DetectedSignal().Connect(&OnPinch);

// Ensure the actor is sensitive to input
myActor.SetProperty(Actor::Property::SENSITIVE, true);
```

> Platform-level detail: Advanced gesture configuration, such as specific recognition algorithms or platform-specific gesture customization, may require platform-level detail. Please refer to the Platform Guide for details on customizing the input pipeline.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/gesture-types)
