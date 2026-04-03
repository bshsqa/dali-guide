---
id: gesture-detection
title: "Gesture Detection"
sidebar_label: "Gesture Detection"
---
## Overview of [Gesture Detection](./gesture-detection.md)

[Gesture Detection](./gesture-detection.md) in DALi is the high-level semantic layer that translates raw, multi-point touch input streams into meaningful human-computer interaction patterns. While raw touch [events](./events.md) provide granular coordinate data, gesture detectors abstract this complexity by tracking touch sequences over time and space to identify patterns like pinches, rotations, or taps.

You should use the `GestureDetector` system whenever your application requires complex touch interaction that exceeds simple "down/move/up" logic. It is distinct from raw touch [events](./events.md) because it handles state machines internally (e.g., distinguishing between a slow drag and a fast swipe) and provides pre-calculated semantic data such as scale factors for pinches or rotation angles for two-finger twists.

## Gesture Processing Pipeline

The gesture pipeline is a multi-threaded architecture designed to maintain UI responsiveness by offloading heavy filtering from the main event loop. Raw input [events](./events.md) are first captured by the platform's input backend and pushed into the DALi event thread for initial filtering and noise reduction.

Once processed, the input is converted into gesture-specific data packets and dispatched to the main thread, where the `GestureDetector` instances perform hit-testing against the scene graph. If a detector identifies a gesture that matches its configuration, it emits a signal to the application-level callback. This pipeline ensures that gesture recognition logic does not block the render loop, maintaining smooth frame rates even during intensive input sequences.

## Supported [Gesture Types](./gesture-types.md)

DALi provides a suite of specialized detectors, each inheriting from the base `GestureDetector` class. Each detector is configured by attaching it to an `Actor`, which serves as the hit-test target for that gesture.

### TapGestureDetector
The `TapGestureDetector` recognizes a single or multi-finger tap. It provides information regarding the number of taps and the coordinate of the gesture.

```cpp
// Example: Creating a Tap Gesture Detector
Dali::TapGestureDetector detector = Dali::TapGestureDetector::New(1); // 1-finger tap
detector.DetectedSignal().Connect(this, &MyClass::OnTap);
detector.Attach(myActor);
```

### PanGestureDetector
The `PanGestureDetector` tracks linear motion across the screen, providing velocity, displacement, and state (Started, Continuing, Finished).

```cpp
// Example: Creating a Pan Gesture Detector
Dali::PanGestureDetector detector = Dali::PanGestureDetector::New();
detector.DetectedSignal().Connect(this, &MyClass::OnPan);
detector.Attach(myActor);
```

### PinchGestureDetector
The `PinchGestureDetector` calculates the scale factor between two touch points, ideal for zooming operations.

### RotationGestureDetector
The `RotationGestureDetector` tracks the angular change between two touch points, reporting the total rotation and the rotation since the last update.

### LongPressGestureDetector
The `LongPressGestureDetector` triggers after a sustained touch on an actor for a configurable duration.

## Detector Lifecycle and Thread Safety

`GestureDetector` objects are reference-counted `Handle` types. They remain alive as long as there is at least one reference held by the application or the scene graph.

> Warning: While the internal gesture processing occurs on the event thread, signals emitted by `GestureDetector` are invoked on the **main thread**. All UI state changes within signal callbacks must be thread-safe regarding the main loop. 

To clean up a detector, call `Detach()` on the actor or reset the handle. If a detector is not detached, it remains registered with the hit-testing system, potentially consuming resources and interfering with propagation.

## Gesture Interception and Event Propagation

Gesture detection works in conjunction with the DALi hit-testing mechanism. When a gesture is detected, the framework performs a hit-test to find the actor that should receive the event. 

If multiple actors have detectors registered, the event follows the visual hierarchy (Z-order) unless explicitly consumed. Once a detector identifies a gesture, it can "consume" the event, preventing further propagation to other detectors or lower-level touch events.

→ See: [Events-Core] for basic bubbling rules.

## Integration with Actor Hierarchies

Integrating gesture detection requires attaching the detector instance to a specific `Actor`. The actor must have a size and be visible to be hit-tested by the detector.

```cpp
// Integrating a gesture with an actor
Dali::Actor myActor = Dali::Actor::New();
myActor.SetSize(100.0f, 100.0f);
myActor.SetParentOrigin(Dali::ParentOrigin::CENTER);

Dali::PanGestureDetector panDetector = Dali::PanGestureDetector::New();
panDetector.Attach(myActor);

// Connect callback
panDetector.DetectedSignal().Connect(this, &MyClass::OnPanDetected);

// Ensure the actor is added to the stage to receive events
Dali::Stage::GetCurrent().Add(myActor);
```

> Note: If an actor's `Opacity` is 0 or it is hidden, it will fail hit-tests. Always verify the `Sensitive` property of the actor when debugging gesture detection issues.

## Performance Optimization Guidelines

To minimize input latency and maintain high performance during gesture sequences:

1. **Limit Detector Count:** Each detector adds overhead to the hit-test pass. Only attach detectors to actors that strictly require interaction.
2. **Avoid Heavy Logic in Callbacks:** Since gesture signals fire on the main thread, perform expensive calculations (like heavy data processing) asynchronously or use an `IdleCallback` to defer work.
3. **Use Specific Hit-Testing:** If you have deep hierarchies, use `SetProperty(Actor::Property::SENSITIVE, false)` on non-interactive child actors to prune the hit-test tree early.
4. **Coordinate Mapping:** Use `Actor::ScreenToLocal()` within the callback to convert raw touch coordinates into the actor's local space, avoiding expensive coordinate transformations if the actor's world transform hasn't changed.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/gesture-detection)
