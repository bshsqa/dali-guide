---
id: signals
title: "signals"
sidebar_label: "signals"
---
## Introduction to Signals

Signals in DALi provide a robust implementation of the Observer pattern, allowing objects to broadcast [events](./events.md) to an arbitrary number of interested listeners. By decoupling the event producer from the subscriber, [signals](./signals.md) enable a reactive programming model essential for UI frameworks where user inputs, lifecycle changes, and [animation](./animation.md) states must trigger cascading logic without tight coupling.

You should use [signals](./signals.md) whenever your application needs to respond to changes in the DALi [object](./object.md) graph, such as touch interactions or scene changes. Unlike direct method invocation, [signals](./signals.md) support multiple connections, connection management, and safe disconnects, making them the distinct backbone of asynchronous communication within the DALi engine.

## Internal Signal Architecture

The signal architecture relies on a specialized PIMPL (Pointer to Implementation) structure to maintain binary compatibility and encapsulate the engine's internal event-routing logic. Each signal [object](./object.md) acts as a handle to an underlying implementation that manages the list of subscribers and the logic for event dispatching.

The `BaseSignal` acts as the primary interface for managing slot connections. The implementation details are hidden within the engine, ensuring that the heavy lifting of signal emission—iterating through thousands of potential observers—remains performant and memory-efficient.

## Callback Mechanism and Functors

DALi utilizes a flexible callback hierarchy centered around `CallbackBase` and specialized `FunctorDelegate` templates. This mechanism allows you to bind member functions, static functions, or lambda expressions to signal emissions, effectively bridging the gap between specific event signatures and your application logic.

### Binding Member Functions
To connect a member function to a signal, use the provided delegate factories to wrap the instance and the method pointer. This ensures that the callback is correctly typed and associated with the specific [object](./object.md) lifetime.

```cpp
// Example: Connecting a member function to a touch signal
class MyHandler {
public:
  bool OnTouch(Actor actor, const TouchEvent& event) {
    return true;
  }
};

// Assuming 'actor' is an initialized Dali::Actor
MyHandler handler;
actor.TouchedSignal().Connect(&handler, &MyHandler::OnTouch);
```

> Note: When binding member functions, ensure the object instance (`&handler`) outlives the signal connection; otherwise, the signal will attempt to invoke a method on a destroyed object.

## Connection Management

Connection management is handled via the `ConnectionTracker` interface, which tracks the association between a signal and a slot. This mechanism is critical for preventing dangling pointers, as it automatically disconnects signals when the tracking object is destroyed.

When you connect a callback, the system returns a `Connection` handle. Developers can use this handle to manually query the connection status or explicitly sever the link if the event listener is no longer required.

## Thread Safety and Emit Guards

DALi signals are primarily designed to be invoked from the main event loop thread where the scene graph lives. While some internal structures are thread-safe, external signal emissions across thread boundaries require explicit synchronization or dispatching to the main thread.

The `EmitGuard` utility provides an essential safeguard for recursive signal emissions. By placing an `EmitGuard` within a signal handler, you ensure that the state of the signal's observer list remains consistent even if a handler attempts to connect or disconnect other observers during the current dispatch cycle.

## Dispatcher Patterns

Dispatchers act as the bridge between the high-level signal trigger and the low-level execution of your callback functors. They handle the marshaling of arguments from the engine's internal data structures into the parameters expected by your specific callback function.

This allows the framework to support varying arity (number of arguments) while maintaining strict type safety at compile time. By using `FunctorDispatcher`, the engine can abstract away the difference between a listener requiring an `Actor` parameter and one requiring a complex event structure.

## Integration API for Render Callbacks

The Integration API provides specialized signal-like hooks for the rendering pipeline. These are primarily used by internal engine components or low-level plugins that need to synchronize with the GPU frame cycle.

`RenderCallback` allows developers to inject logic that executes immediately prior to or following a draw call. This is distinct from standard user-level signals because it operates within the strict timing constraints of the frame-rendering thread, where blocking operations must be avoided at all costs.

## Lifecycle and Destruction Handling

To ensure orderly resource cleanup, DALi utilizes `Destroyer` and `FunctorDestroyer` utilities. These components ensure that when a signal is destroyed, or when an object is removed from the scene graph, any associated functors are cleaned up to prevent memory leaks.

When working with custom functors, always ensure that your cleanup logic is exception-safe. The `Destroyer` system handles the destruction of internal delegate objects, ensuring that memory management remains transparent to the application developer.

→ See: [Actor]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/signals)
