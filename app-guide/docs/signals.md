---
id: signals
title: "signals"
sidebar_label: "signals"
---
## Introduction to Signals and Callbacks

Signals in DALi are the framework's implementation of the observer pattern, providing a type-safe mechanism to notify your application logic when specific [events](./events.md) occur. They facilitate event-driven communication by decoupling the source of an event (such as an `Actor` being touched) from the code that handles that event.

You should use [signals](./signals.md) whenever your application needs to respond to changes in the UI state or user input, such as handling clicks, layout changes, or lifecycle transitions. What makes DALi [signals](./signals.md) distinct is their tight integration with the [object](./object.md) lifecycle, allowing for memory-safe connections that prevent dangling pointers when objects are destroyed.

## Connecting to Signals

Connecting to a signal allows you to register a callback function that will be executed whenever the signal is emitted. This is the primary way to hook your business logic into the framework's event loop.

### Establishing a Connection
To connect to a signal, you typically invoke the `.Connect()` method on a signal [object](./object.md) returned by an `Actor` getter method (e.g., `TouchedSignal()`). The connection process ensures that the signal maintains a reference to your handler until explicitly disconnected or the handler's lifetime ends.

```cpp
// Example: Connecting to a touch signal
bool OnTouch(Actor actor, const TouchEvent& event) {
  // Handle touch logic here
  return true;
}

void Setup(Actor myActor) {
  myActor.TouchedSignal().Connect(&OnTouch);
}
```

> Note: Signals are typically provided as return values from specific methods on objects rather than as direct members.

## Creating Custom Callbacks

Custom callbacks are the functions or functors invoked when a signal triggers. They must match the signature defined by the specific signal they are connected to, ensuring type safety during emission.

### Using Global or Static Functions
For simple logic, connecting a standard function pointer is the most direct approach. This is ideal for stateless handlers that do not require access to private class member data.

```cpp
bool MyCallback(Actor actor, const TouchEvent& event) {
  // Logic executed upon emission
  return true;
}

// Connecting
myActor.TouchedSignal().Connect(&MyCallback);
```

## Managing Signal Connections

Managing the lifetime of your connections is critical to building a stable application. If a signal attempts to invoke a callback on an object that has already been destroyed, it will lead to memory corruption.

### The ConnectionTracker
The `ConnectionTracker` is a mixin class that automatically tracks all connections made by an object. When the `ConnectionTracker` is destroyed, it automatically disconnects any signals currently pointing to its member functions.

```cpp
class MyHandler : public ConnectionTracker {
public:
  void Initialize(Actor actor) {
    actor.TouchedSignal().Connect(this, &MyHandler::OnTouch);
  }

  bool OnTouch(Actor actor, const TouchEvent& event) {
    return true;
  }
};
```

> Warning: Always derive from `ConnectionTracker` when connecting to class member functions to avoid undefined behavior during object deallocation.

## Handling Variadic Arguments

DALi signals are designed to pass relevant context to the subscriber via function arguments. These arguments vary based on the signal type, providing everything from mouse coordinates to state change flags.

### Processing Event Data
When a signal emits, it packs relevant data into standard C++ types. You must ensure your callback signature matches the number and type of arguments expected by that specific `SignalType`.

```cpp
// Example: Handling OnRelayout
void OnRelayout(Actor actor) {
  Vector3 size = actor.GetTargetSize();
  // Process the actor's new layout dimensions
}

myActor.OnRelayoutSignal().Connect(&OnRelayout);
```

## Working with Return Values

Some signals in DALi expect a return value from the callback to determine the subsequent behavior of the framework. For instance, returning `true` from a touch signal often indicates that the event has been consumed and should not be propagated further.

### Implementing Return-Type Logic
When connecting to signals that expect a return value, your callback must explicitly return the appropriate type (usually `bool`). Failure to return a value in such functions is a logic error that may result in unexpected event bubbling.

```cpp
bool MyTouchHandler(Actor actor, const TouchEvent& event) {
  // Returning true prevents the event from passing to actors behind this one
  return true; 
}
```

## Best Practices for Signal Safety

Robust UI development depends on predictable signal emission and safe connection management. Adhering to these patterns minimizes the risk of crashes and memory leaks.

*   **Always use ConnectionTracker:** Never connect class methods to signals without inheriting from `ConnectionTracker`.
*   **Keep Callbacks Lightweight:** Signals run on the main event loop; performing heavy computations inside a signal callback will result in dropped frames and an unresponsive UI.
*   **Avoid Cycles:** Be cautious about modifying the signal source (e.g., hiding an actor) within a callback triggered by that same actor, as this can cause unpredictable recursion or logical deadlocks.
*   **Verify Object Validity:** Always check if an `Actor` is valid within the callback, as the state of the UI may have changed between the signal being queued and being executed.

→ See: [Actor] for more on managing [object](./object.md) states and hierarchy.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/signals)
