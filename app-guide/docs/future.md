---
id: future
title: "Future"
sidebar_label: "Future"
---
## Introduction to Dali::[Future](./future.md)

The `Dali::Future` class serves as a synchronization primitive that acts as a handle for the eventual result of an asynchronous operation. Unlike raw thread management, `Dali::Future` provides a standardized way to query, wait for, or retrieve the result of a task performed in a background worker context without manually managing thread handles or condition variables.

Use `Dali::Future` when you need to trigger a non-blocking background computation—such as data processing or complex resource loading—and require a clean, type-safe mechanism to retrieve the final output once the work is complete. It is distinct from other [threading](./threading.md) variants by its focus on "value-passing," allowing the main UI thread to remain responsive while polling for or awaiting the completion of a background task.

→ See: [Dali::Thread]

## Creating and Managing Futures

Managing the lifecycle of a `Dali::Future` is essential for memory efficiency and ensuring that results are only accessed when they are ready. Since `Dali::Future` is a handle-based [object](./object.md), maintaining a valid reference is necessary until the background operation concludes.

### IsValid
The `IsValid` method checks if the future handle currently points to a valid asynchronous operation. This should be used after receiving a future to ensure that the task was successfully dispatched.

*   **Returns:** `bool` - `true` if the handle is valid and associated with an operation; `false` otherwise.

### Reset
The `Reset` method clears the reference to the internal operation. This is useful for cleaning up resources when the result of a task is no longer required, preventing memory leaks in the event that the background task is discarded.

```cpp
void HandleTaskCleanup(Dali::Future future) 
{
  if (future.IsValid()) 
  {
    // If the task is cancelled or no longer needed:
    future.Reset();
  }
}
```

## Retrieving Results

The `Get` method is the primary mechanism for accessing the computed result of an asynchronous task. This method interacts with the underlying state of the future to provide the encapsulated value once the background work has finalized.

### Get
Use `Get` to retrieve the result of the asynchronous operation. Note that calling `Get` on a future that has not yet completed will typically return an empty or default-initialized value, depending on the implementation state.

*   **Returns:** The result type associated with the future.

> **Note:** Always ensure the background task has finished before attempting to use the returned value to avoid handling partial or empty data.

```cpp
void OnTaskFinished(Dali::Future future) 
{
  if (future.IsValid()) 
  {
    auto result = future.Get();
    // Process the result returned from the background thread
  }
}
```

## Synchronizing with Wait

When the application logic cannot proceed without the result of a background operation, the `Wait` method provides a deterministic way to halt execution until the task finishes. 

### Wait
The `Wait` method blocks the current thread until the associated asynchronous operation completes. This should be used sparingly, as blocking the main UI thread with `Wait` can cause frame drops and noticeable input latency.

*   **Preconditions:** The future must be valid. Calling `Wait` on an invalid future results in undefined behavior.

```cpp
void ProcessDataBlocking(Dali::Future future) 
{
  if (future.IsValid()) 
  {
    // Block the thread until the background worker finishes
    future.Wait();
    
    // Now safe to retrieve the result
    auto data = future.Get();
  }
}
```

> **Warning:** Never call `Wait` on the main DALi event loop thread unless you are certain the background operation will finish almost instantaneously. Excessive use of `Wait` will lead to application "jank" and may trigger watchdog timer shutdowns.

## Best Practices for Asynchronous UI Tasks

To maintain high performance in a DALi-based application, favor reactive patterns over synchronous blocking. Instead of using `Wait` on the main thread, design your UI logic to poll the status of the future or utilize callback mechanisms provided by the specific asynchronous API that generated the future.

*   **Prefer Polling:** Periodically check the status of a long-running task during a frame update (e.g., via a `Dali::Timer` or `Dali::Actor::Signal`) rather than blocking the UI thread.
*   **Handle Lifetime:** Always check `IsValid()` before invoking `Get()` or `Wait()` to ensure the background task was not destroyed or invalidated by a previous system event.
*   **UI Thread Safety:** Remember that any data retrieved from a `Dali::[Future](./future.md)` that involves manipulating UI elements must be applied back to the main thread, adhering to the standard DALi single-thread [update](./update.md) rule.

> **Platform Detail:** Advanced [threading](./threading.md) primitives and task schedulers that integrate directly with the DALi event loop are handled via platform-level details. Please refer to the platform [threading](./threading.md) guide for information on custom task queueing.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/future)
