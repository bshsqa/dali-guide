---
id: conditional-wait
title: "ConditionalWait"
sidebar_label: "ConditionalWait"
---
## Introduction to [ConditionalWait](./conditional-wait.md)

`Dali::ConditionalWait` is a synchronization primitive designed for thread coordination where one thread must pause execution until a specific state change or event occurs. Unlike simple mutexes or semaphores, `ConditionalWait` provides a mechanism for threads to sleep efficiently while waiting for a condition to be signaled by another thread, minimizing CPU consumption during idle periods.

Use `ConditionalWait` when you need to implement producer-consumer patterns or gatekeeping mechanisms within the DALi engine where a background worker must react dynamically to engine state updates or input processing. It is distinct from other synchronization tools by its tight integration with the `ScopedLock` pattern, ensuring atomicity between checking a state and entering a wait state.

## Lifecycle and Object Management

The `ConditionalWait` [object](./object.md) is designed for direct stack allocation or embedding within larger worker-thread helper objects. The lifecycle is straightforward: upon construction, it initializes the internal synchronization primitives (mutex and condition variable) required for the engine's [threading](./threading.md) environment.

### Constructor and Destructor
The `ConditionalWait()` constructor initializes the internal `ConditionalWaitImpl` structures. The destructor `~ConditionalWait()` performs necessary cleanup of these resources; ensure that no threads are currently suspended on the wait [object](./object.md) before the [object](./object.md) goes out of scope to avoid undefined behavior.

```cpp
// Example: Basic lifecycle management
{
  Dali::ConditionalWait workerWait;
  // Use workerWait for thread coordination...
} // workerWait is destroyed here, cleaning up internal primitives.
```

> Note: `[ConditionalWait](./conditional-wait.md)` is not intended for shared ownership or reference counting; it is a concrete class managing raw synchronization primitives.

## Core Synchronization Primitives

The core functionality of `[ConditionalWait](./conditional-wait.md)` revolves around the `Wait()` and `Notify()` methods. These methods enforce the classic monitor pattern, where a `ScopedLock` is used to guard the condition being checked.

### Wait and Notify
`Wait()` suspends the calling thread, releasing the lock until `Notify()` is called. `Notify()` awakens one thread that is currently blocked on the `[ConditionalWait](./conditional-wait.md)` object.

*   **Wait(const ScopedLock &scope)**: Blocks the current thread. The `scope` must represent a valid lock on this `[ConditionalWait](./conditional-wait.md)` instance.
*   **Notify(const ScopedLock &scope)**: Signals a waiting thread to wake up.

```cpp
Dali::ConditionalWait condition;
Dali::ConditionalWait::ScopedLock lock(condition);

// Worker Thread
void Worker() {
  Dali::ConditionalWait::ScopedLock lock(condition);
  condition.Wait(lock); 
  // Execution resumes after notify
}

// Signaling Thread
void Signal() {
  Dali::ConditionalWait::ScopedLock lock(condition);
  condition.Notify(lock);
}
```

## Timed Synchronization with TimePoint

When thread blocking must not last indefinitely, `WaitUntil` allows the developer to specify a deadline. This is essential for preventing deadlocks in high-priority engine loops where a failure to receive a notification should result in a timeout and fallback behavior.

### WaitUntil
`WaitUntil` takes a `TimePoint` (the absolute deadline) and a `ScopedLock`. If the `TimePoint` is reached before a `Notify()` occurs, the thread resumes execution.

*   **scope**: A `ScopedLock` associated with the `[ConditionalWait](./conditional-wait.md)` instance.
*   **timePoint**: An absolute time value defined by `TimePoint` representing when the block should terminate regardless of signal status.

```cpp
void TimedWorker(Dali::ConditionalWait& condition) {
  Dali::ConditionalWait::ScopedLock lock(condition);
  Dali::ConditionalWait::TimePoint deadline = /* ... calculate future time ... */;
  
  condition.WaitUntil(lock, deadline);
  // Proceed with logic, checking if the condition was met
}
```

## Thread Safety and Integration Patterns

`[ConditionalWait](./conditional-wait.md)` is designed to be thread-safe regarding the orchestration of wait queues. However, the logic *guarded* by the `ScopedLock` remains the responsibility of the developer. Always access the data that determines your "condition" while holding the `ScopedLock`.

### The ScopedLock Pattern
The `ScopedLock` acts as a RAII wrapper for the internal mutex. When instantiated, it locks the `[ConditionalWait](./conditional-wait.md)` mutex; when it goes out of scope, it unlocks it.

*   **Constructor**: Takes the `[ConditionalWait](./conditional-wait.md)` instance to lock.
*   **GetLockedWait()**: Returns a reference to the `[ConditionalWait](./conditional-wait.md)` being guarded.

```cpp
void ThreadSafeTask(Dali::ConditionalWait& condition) {
  {
    Dali::ConditionalWait::ScopedLock lock(condition);
    // Access protected data here
  } // Mutex automatically released
}
```

## Monitoring and Wait Queue Diagnostics

In complex threading environments, it is often necessary to debug thread contention or verify that worker threads are correctly responding to signals. `GetWaitCount` provides real-time visibility into the current thread queue.

### GetWaitCount
Returns the number of threads currently blocked on the `[ConditionalWait](./conditional-wait.md)` instance.

*   **Return**: `unsigned int` representing the number of waiting threads.

```cpp
void DiagnosticCheck(Dali::ConditionalWait& condition) {
  unsigned int pending = condition.GetWaitCount();
  if (pending > 0) {
    // Log contention or trigger safety recovery
  }
}
```

> Warning: `GetWaitCount` provides a point-in-time snapshot. In highly concurrent scenarios, the value may change immediately after the function returns; use it primarily for telemetry or debugging heuristics.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/conditional-wait)
