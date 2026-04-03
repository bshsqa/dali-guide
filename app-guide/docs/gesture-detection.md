---
id: gesture-detection
title: "Gesture Detection"
sidebar_label: "Gesture Detection"
---
## Introduction to [Gesture Detection](./gesture-detection.md)

Gesture detection in DALi abstracts raw touch [events](./events.md) into high-level, semantic interactions, allowing developers to focus on application logic rather than low-level coordinate calculation. Use this module when your UI requires complex interactions like pinching to zoom, rotating objects, or detecting multi-finger pans, which are significantly more labor-intensive to implement via raw touch event monitoring.

Gesture detection is distinct from standard touch event handling because it maintains internal state machines to identify patterns across time and multiple touch points. While raw touch [events](./events.md) provide granular data, gesture detectors offer normalized data structures representing completed or ongoing user intents.

## Configuring Gesture Detectors

To capture user interaction, you must instantiate a specific gesture detector (e.g., `TapGestureDetector`, `PanGestureDetector`) and attach it to an `Actor`. Each detector acts as a middleware that listens to input [events](./events.md) targeted at the actor and processes them into recognized gestures.

### Creating and Attaching Detectors
You instantiate a detector by calling its static `New()` method and attaching it to the target actor using the `Attach()` method. Once attached, the detector begins monitoring touch inputs that occur within the actor's boundaries.

```cpp
// Example: Creating a TapGestureDetector
Dali::TapGestureDetector tapDetector = Dali::TapGestureDetector::New();
tapDetector.Attach(myActor);

// Example: Creating a PanGestureDetector
Dali::PanGestureDetector panDetector = Dali::PanGestureDetector::New();
panDetector.Attach(myActor);
```

> Note: An actor can have multiple gesture detectors attached simultaneously, allowing a single UI component to respond to taps, pans, and pinches concurrently.

## Handling Gesture Signals

Gesture detectors communicate with your application via signals emitted when a specific interaction pattern is recognized. By connecting a callback function to these signals, you can trigger application logic based on the processed gesture data provided by the event.

### Connecting to Signal Callbacks
Each gesture detector provides a signal (e.g., `DetectedSignal()`) that notifies the application when the specific gesture is finalized or transitions state. The callback receives the detector handle and a structure containing the gesture data.

```cpp
void OnTap(Dali::TapGestureDetector& detector, const Dali::TapGesture& gesture)
{
  // Logic executed upon a successful tap
}

// Connecting the signal
tapDetector.DetectedSignal().Connect(&OnTap);
```

## Gesture State Management

Managing gesture states is critical for interactions that span multiple frames, such as continuous panning or pinching. The gesture data structure contains a `state` property, allowing you to distinguish between the initiation, update, and completion phases of an interaction.

### Tracking State Transitions
When implementing continuous gestures, always verify the state of the `Gesture` object passed to your callback. This allows your application to perform cumulative updates during the "continuing" state and final processing upon the "finished" or "cancelled" states.

```cpp
void OnPan(Dali::PanGestureDetector& detector, const Dali::PanGesture& gesture)
{
  switch(gesture.state)
  {
    case Dali::GestureState::Started:
      // Initialize transformation tracking
      break;
    case Dali::GestureState::Continuing:
      // Apply delta transformations based on gesture.displacement
      break;
    case Dali::GestureState::Finished:
      // Finalize animation or logic
      break;
    default:
      break;
  }
}
```

## Advanced Gesture Coordination

When multiple detectors are attached to a single actor, you may encounter input conflicts where one detector should take precedence over another. DALi allows you to manage these interactions by configuring detector properties to ensure that ambiguous inputs are routed correctly to the intended interaction logic.

### Setting Detection Parameters
You can customize the sensitivity and requirements for each detector. For example, you can define the minimum number of touches required for a pan or the duration tolerance for a tap. 

*   **WHAT:** Configuring properties (e.g., `SetMinimumTouches`) restricts or broadens the trigger conditions.
*   **WHY:** Use this to prevent accidental triggers or to differentiate between one-finger and two-finger interactions on the same component.
*   **HOW:** Use the specific setter methods available on the detector instance.

```cpp
// Configuring a Pan detector for two-finger only
Dali::PanGestureDetector panDetector = Dali::PanGestureDetector::New();
panDetector.SetMinimumTouches(2);
panDetector.Attach(myActor);
```

## Best Practices and Troubleshooting

To ensure a responsive and intuitive UI, adhere to standard interaction design patterns and monitor the performance of your gesture callbacks.

*   **Avoid Expensive Logic:** Gesture callbacks, especially for high-frequency updates like `Pan` or `Pinch`, run on the main thread; avoid heavy synchronous operations inside these functions.
*   **Consistency:** Use consistent state-handling logic across your application to ensure that `Cancelled` states are handled gracefully (e.g., reverting a half-finished transformation).
*   **Sibling Components:** For managing focus and input pass-through for complex widgets, → See: [Actor].
*   **Platform-Level Detail:** If you require custom gesture recognition logic beyond the provided set (e.g., complex multi-stroke patterns), this constitutes a platform-level detail requiring advanced event processing; please refer to the platform guide for custom input pipeline integration.

> Warning: Always ensure that you disconnect [signals](./signals.md) or release detector references when an actor is destroyed to prevent memory leaks and dangling callback references.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/gesture-detection)
