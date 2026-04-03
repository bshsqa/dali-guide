---
id: events
title: "events"
sidebar_label: "events"
---
## Introduction to DALi Events

The DALi event system is the central mechanism for capturing, processing, and propagating user interactions throughout your application. By decoupling low-level input [signals](./signals.md) from UI logic, it enables developers to create responsive, interactive experiences with minimal boilerplate.

You should use the [events](./events.md) module whenever your application needs to respond to user touch, hover, or complex gesture inputs. It is distinct because it integrates directly with the DALi scenegraph, allowing you to route input to specific actors based on their position and visibility rather than global coordinates.

## Understanding Event Propagation

Event propagation in DALi follows a structured path from the physical input surface through the hit-testing process to individual target actors. Once an event is captured, it is processed by the relevant `GestureDetector` or event handler, which then triggers [signals](./signals.md) that your application can observe to execute business logic.

This pipeline ensures that interactions are routed efficiently while allowing developers to intercept or consume [events](./events.md) at specific nodes in the scenegraph hierarchy.

## Sub-Components Overview

The event system is modularized into three key areas to handle different interaction paradigms:

* **[Gesture Detection](./gesture-detection.md)**: Provides the framework for analyzing raw touch streams to identify high-level user intentions such as taps, pans, or pinches. → See: [[Gesture Detection](./gesture-detection.md)]
* **[Gesture Types](./gesture-types.md)**: Defines the structures representing the output of gesture recognition, providing data such as state, timing, and source information. → See: [[Gesture Types](./gesture-types.md)]
* **[Input Events](./input-events.md)**: Manages raw interaction data including touch and hover points, allowing for granular control over pointer-based input. → See: [[Input Events](./input-events.md)]

## Hit-Testing Mechanics

Hit-testing is the process of determining which actor sits beneath a specific coordinate at a given point in time. DALi automatically performs this calculation to populate event data, allowing your [signals](./signals.md) to respond to the specific actor that was touched or hovered.

### Using Hit-Testing with HoverEvent
The `HoverEvent` allows you to query which actor is currently under the pointer, which is essential for implementing UI feedback like button highlights.

**What:** The `GetHitActor` method returns the `Actor` instance that occupies the coordinate associated with a specific hover point.
**Why:** Use this to determine if a mouse or stylus is hovering over a specific interactive element.
**How:** Call `GetHitActor(std::size_t point)` where `point` is the index of the hover point. It returns an `Actor` handle; if no actor is hit, an empty handle is returned.

```cpp
// Example: Checking for hover on an actor
void OnHover(const Dali::HoverEvent& event) {
  for (std::size_t i = 0; i < event.GetPointCount(); ++i) {
    Dali::Actor hitActor = event.GetHitActor(i);
    if (hitActor) {
      // Respond to hover state change on the specific actor
    }
  }
}
```

> Note: Hit-testing relies on the actor's world-space position and size. Ensure your actors are correctly added to the scene, or `GetHitActor` will return an empty handle.

## Best Practices for Interaction Design

To build robust interfaces, always prefer high-level `GestureDetectors` over raw touch event processing where possible. Use `Attach` and `Detach` to manage which actors are listening for input to maintain performance, and always ensure you are not blocking the main event loop with heavy calculations inside signal callbacks.

### Managing Gesture Detectors
The `GestureDetector` class allows you to maintain clean control over which actors respond to specific interaction patterns.

**What:** The `Attach` and `Detach` methods define the relationship between an interaction pattern and a visual actor.
**Why:** Use these to dynamically enable or disable touch responsiveness on UI elements.
**How:** Pass an `Actor` handle to `Attach(Actor)` to bind it. Use `Detach(Actor)` or `DetachAll()` to stop detection.

```cpp
// Example: Attaching an actor to a detector
void SetupInteraction(Dali::GestureDetector detector, Dali::Actor myButton) {
  // Attach the actor to the detector to start receiving callbacks
  detector.Attach(myButton);
  
  // Later, if the button becomes disabled:
  if (detector.GetAttachedActorCount() > 0) {
    detector.Detach(myButton);
  }
}
```

> Warning: Calling `CancelAllOtherGestureDetectors()` will immediately stop all other active gesture recognition; use this sparingly to prevent unintended cancellation of secondary interaction streams in complex UIs.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/events)
