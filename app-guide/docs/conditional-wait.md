---
id: conditional-wait
title: "ConditionalWait"
sidebar_label: "ConditionalWait"
---
## Introduction to Conditional Wait

The `ConditionalWait` class is a synchronization primitive within the DALi [threading](./threading.md) module designed to manage task gating where threads must pause execution until a specific state transition occurs. Unlike simple mutexes that provide mutual exclusion, `ConditionalWait` allows a thread to sleep efficiently while waiting for a notification from another part of the application, significantly reducing CPU overhead compared to busy-waiting or polling.

Use `ConditionalWait` when you have producer-consumer scenarios or complex state dependencies where a background worker thread needs to wait for the UI thread (or another worker) to satisfy a condition before proceeding. This is distinct from standard mutexes, which merely protect resource access, as it enables thread signaling.
→ See: [[Mutex](./mutex.md)]

## Initializing the [ConditionalWait](./conditional-wait.md) Object

The `ConditionalWait` [object](./object.md) is initialized as a standard stack or heap-allocated instance, ready to be shared between threads. It acts as a gatekeeper for your synchronization logic.

### Constructor
To initialize the [object](./object.md), simply instantiate the class. It requires no configuration parameters upon construction.

```cpp
#include <dali/devel-api/threading/conditional-wait.h>

// Example: Initializing a ConditionalWait object in a worker class
class WorkerTask {
public:
    WorkerTask() : mCondition() {}
private:
    Dali::ConditionalWait mCondition;
};
```

> Note: Ensure that the `[ConditionalWait](./conditional-wait.md)` instance remains in scope for the duration of any thread's lifecycle that might call `Wait()` or `Notify()` on it.

## Synchronous Waiting Patterns

Waiting patterns allow threads to halt execution until they receive a signal to continue, preventing unnecessary processing while idling.

### Wait
The `Wait()` method suspends the current thread until the `[ConditionalWait](./conditional-wait.md)` object is signaled.

*   **WHAT:** It blocks the calling thread indefinitely until `Notify()` is called from another thread.
*   **WHY:** Use this when a thread has no further work to perform until an external state change occurs.
*   **HOW:** Call `Wait()` without parameters. The calling thread will yield execution and remain blocked until it is awakened.

```cpp
void WorkerThreadFunction(Dali::ConditionalWait& condition, Dali::Mutex& mutex) {
    Dali::Mutex::ScopedLock lock(mutex);
    // Wait until the main thread signals that data is ready
    condition.Wait(mutex); 
}
```

### WaitUntil
The `WaitUntil()` method suspends the current thread until a signal is received or a specific timeout is reached.

*   **WHAT:** It blocks the thread until `Notify()` is triggered or the defined duration expires.
*   **WHY:** This is essential for preventing permanent deadlocks if the signaling logic fails or if you need to perform periodic checks.
*   **HOW:** Pass a duration type representing the maximum wait time.

```cpp
void TimedWorker(Dali::ConditionalWait& condition, Dali::Mutex& mutex) {
    Dali::Mutex::ScopedLock lock(mutex);
    // Wait for up to 500 milliseconds
    bool signaled = condition.Wait(mutex, Dali::Time::Milliseconds(500));
    if(!signaled) {
        // Handle timeout logic
    }
}
```

## Signaling and Notification

Signaling is the mechanism by which blocked threads are woken up to resume execution.

### Notify
The `Notify()` method sends a signal to one of the threads currently blocked by a `Wait()` call on this object.

*   **WHAT:** It unblocks one waiting thread. If no threads are waiting, the signal is ignored.
*   **WHY:** Call this when your application state has changed such that a waiting thread can now complete its task.
*   **HOW:** Call `Notify()` to wake a single thread.

```cpp
void NotifyWorker(Dali::ConditionalWait& condition) {
    // Notify the waiting thread that it can proceed
    condition.Notify();
}
```

## Monitoring Wait State

Monitoring the wait state provides insights into the synchronization health of your application, particularly useful for debugging thread-heavy logic.

### GetWaitCount
This method returns the number of threads currently blocked on this conditional variable.

*   **WHAT:** It returns the count of threads currently waiting on the signal.
*   **WHY:** Useful for telemetry, load monitoring, or verifying that background workers have reached their waiting state as expected.
*   **HOW:** Call `GetWaitCount()` to receive a `size_t` representing the number of threads.

```cpp
void DebugWaitStatus(Dali::ConditionalWait& condition) {
    size_t count = condition.GetWaitCount();
    if(count > 0) {
        // Log the number of threads waiting
    }
}
```

## Best Practices for Thread Safety

Proper synchronization requires careful management to avoid common pitfalls such as lost wake-up calls and deadlocks.

*   **Use Mutexes:** Always associate your `[ConditionalWait](./conditional-wait.md)` with a `[Mutex](./mutex.md)`. The wait operation is designed to atomically release the mutex while waiting and re-acquire it before returning.
*   **Spurious Wake-ups:** Always wrap your `Wait` call in a `while` loop that checks the condition variable being monitored. Even if signaled, the condition might have been changed by another thread before the current thread re-acquires the mutex.
*   **Order of Operations:** Ensure the state variable (the condition being checked) is modified while the mutex is held, and call `Notify()` only after the state change is visible to other threads.

> Warning: Never rely on `Notify()` being received if the thread is not already inside the `Wait()` call; [signals](./signals.md) are not queued. Always use a shared boolean or state variable to track if the work is actually ready.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/conditional-wait)
