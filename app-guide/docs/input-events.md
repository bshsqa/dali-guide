---
id: input-events
title: "Input Events"
sidebar_label: "Input Events"
---
## [Input Events](./input-events.md) Overview

The `input-events` module provides the foundational C++ interface for capturing and processing raw user interactions within the DALi framework. Developers use these APIs when they need to build custom UI components that require precise control over touch interaction, gesture interpretation, or keyboard navigation, as opposed to relying on high-level pre-built controls.

What makes `input-events` distinct is its role as the bridge between hardware-level input and the actor-based scene graph. While `Touch` and `Key` [events](./events.md) provide the raw data, this module offers the signal-based notification system that allows developers to intercept, filter, and respond to user behavior at any point in the actor hierarchy.

## Handling Touch and Pointer Input

Touch [events](./events.md) are the primary mechanism for interaction on mobile and touch-enabled surfaces. You should use the `TouchedSignal()` of an `Actor` to monitor finger interactions, which are delivered as `TouchData` objects containing coordinate information and state changes.

### Processing TouchData
The `TouchData` [object](./object.md) encapsulates the state of one or more points of contact on the screen. It allows developers to determine if a touch has started, moved, or finished, enabling custom interaction logic.

```cpp
#include <dali/dali.h>

using namespace Dali;

bool OnTouched(Actor actor, const TouchData& event)
{
  // Retrieve the state of the first point of contact
  PointState::Type state = event.GetState(0);
  Vector2 localPos = event.GetLocalPosition(0);

  if(state == PointState::DOWN)
  {
    // Handle initial contact
  }
  return true; // Return true to consume the event
}

// Usage in an Actor
void CreateTouchActor()
{
  Actor myActor = Actor::New();
  myActor.TouchedSignal().Connect(&OnTouched);
}
```

> Note: Returning `true` from a touch callback consumes the event, preventing it from propagating to actors beneath the target in the hit-test order.

## Gesture Detection and Recognition

Gesture detection simplifies the process of interpreting complex touch patterns into semantic actions like taps, pans, or pinches. Use these detectors when you need to trigger logic based on movement patterns rather than raw coordinate tracking.

### Using Gesture Detectors
The framework provides specific detector classes such as `TapGestureDetector`, `PanGestureDetector`, and `PinchGestureDetector`. These act as observers that monitor an actor's touch stream and emit signals only when a specific pattern is matched.

→ See: [GestureDetector]

## Focus Management for Input

In a complex scene, focus management ensures that key events and navigation commands are routed to the intended UI component. You configure focus by setting an actor as "focusable" and managing its state within the `FocusManager`.

### Configuring Focus
When multiple actors can accept input, the `FocusManager` determines which one currently owns the "Focus." This is critical for remote control or keyboard-driven interfaces where visual feedback must reflect the active element.

```cpp
void ConfigureFocus(Actor myButton)
{
  // Enable the actor to receive focus
  myButton.SetKeyboardFocusable(true);
  
  FocusManager& fm = FocusManager::Get();
  fm.SetCurrentFocusActor(myButton);
}
```

## Keyboard and Key Events

Keyboard events handle inputs from physical keyboards, virtual input methods, or directional pads. These are processed via the `KeyEvent` signal emitted by the `Stage`.

### Handling Key Events
The `KeyEvent` object provides the key name and the specific key code, allowing developers to implement shortcut keys or text input navigation.

```cpp
bool OnKey(const KeyEvent& event)
{
  if(event.GetKeyName() == "Enter")
  {
    // Handle Enter key trigger
    return true; 
  }
  return false;
}

void Initialize()
{
  Stage::GetCurrent().KeyEventSignal().Connect(&OnKey);
}
```

## Event Propagation and Consumption

Events in DALi follow a hit-testing order, propagating through the scene graph. Understanding how to stop or allow this flow is essential for creating overlapping UI elements where background objects should remain interactive.

### Controlling Consumption
By returning a boolean value from signal callbacks, you explicitly control whether an event continues to propagate. A return value of `true` [signals](./signals.md) that the event has been handled and should cease propagation.

## Input Event Filtering and Throttling

For high-frequency inputs, such as pan movements or analog joystick values, you may need to apply filtering to avoid unnecessary updates to the scene graph.

### Throttling Logic
You can throttle updates by comparing the timestamp or coordinate delta of consecutive [events](./events.md) within your callback. This ensures that expensive operations (like complex layout recalculations) are only triggered when the input change exceeds a defined threshold or passes a time gate.

> Warning: Excessive filtering can lead to "input lag," where the UI feels disconnected from the user's finger. Always perform only lightweight calculations inside input callbacks.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/input-events)
