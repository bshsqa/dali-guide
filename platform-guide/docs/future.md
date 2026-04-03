---
id: future
title: "Future"
sidebar_label: "Future"
---
## Introduction to Dali::[Future](./future.md)

`Dali::Future` is a specialized synchronization primitive designed to facilitate asynchronous programming within the DALi engine. It serves as a placeholder for a result that is computed by a background task, allowing the main event loop or worker threads to initiate intensive operations without stalling. Unlike generic [threading](./threading.md) primitives, `Dali::Future` is specifically architected to integrate with DALi's internal task scheduling, providing a clean abstraction for retrieving values once the computation has concluded. You should use `Future` when you need to bridge the gap between a long-running background computation and a subsequent dependent operation.

## Asynchronous Result Handling

The `Future` mechanism encapsulates the promise of a value that will be computed in the future, abstracting away the underlying synchronization complexity. It effectively decouples the request for a result from the actual availability of the data, allowing the system to continue executing other tasks until the result is required.

### Retrieval with Get()
The `Get()` method retrieves the result of the asynchronous operation. If the result is not yet available, the calling thread will block until the operation completes and the value is returned.

```cpp
// Example: Retrieving an integer result from a future
Dali::Future<int> myFuture = PerformCalculationAsync();

// ... perform other engine tasks ...

if (myFuture.IsValid()) {
    int result = myFuture.Get(); // Blocks if computation is still pending
    // Use the result here
}
```

> **Warning:** Calling `Get()` from the main UI thread can cause the application to drop frames or become unresponsive if the background task takes longer than a single frame interval. Always ensure that the task associated with the `[Future](./future.md)` has a reasonable execution time, or utilize `Wait()` and checks to verify readiness before calling `Get()`.

## Lifecycle and Memory Management

`Dali::[Future](./future.md)` objects follow standard handle-based memory management semantics within DALi, ensuring that the lifecycle of the underlying asynchronous state is safely managed. Proper management of these objects prevents memory leaks or dangling synchronization points in complex scene-graph operations.

### Resetting State
The `Reset()` method returns the `[Future](./future.md)` object to its initial, uninitialized state. This is essential when the object needs to be reused for subsequent asynchronous operations, clearing any internal state or references to previous results.

```cpp
Dali::Future<int> myFuture = StartTask();
myFuture.Wait();
int val = myFuture.Get();

// Reset for the next usage cycle
myFuture.Reset();
```

## Synchronizing Execution with Wait

The `Wait()` method provides a non-retrieval synchronization point. It ensures that the background task is fully completed before allowing the execution thread to proceed, serving as a robust "fencing" mechanism.

### Usage of Wait()
When you need to ensure completion without needing the return value immediately, `Wait()` is the most efficient synchronization tool.

```cpp
Dali::Future<void> task = LoadLargeResourceAsync();

// Ensure the resource is loaded before proceeding with scene rendering
task.Wait(); 
// Resource is now guaranteed to be ready for use
```

→ See: [Dali::FutureGroup](https://developer.tizen.org) for synchronizing multiple operations simultaneously.

## Validation and State Checking

Before attempting to access the data held by a `[Future](./future.md)`, it is best practice to verify its state. The `IsValid()` method allows developers to determine if the `[Future](./future.md)` is actively tracking a task and contains a meaningful result.

### Checking Validity with IsValid()
Calling `IsValid()` prevents potential errors associated with operating on uninitialized or exhausted `[Future](./future.md)` handles. It should be used as a guard clause before calls to `Get()`.

```cpp
Dali::Future<int> statusFuture = CheckSystemStatus();

if (statusFuture.IsValid()) {
    // It is now safe to proceed with synchronization or retrieval
    statusFuture.Wait();
    int status = statusFuture.Get();
}
```

## Integration Best Practices

Effective use of `[Future](./future.md)` requires a clear strategy for managing threads and avoiding deadlocks. When implementing custom task systems, ensure that the thread executing the `[Future](./future.md)` task is not blocked by a thread waiting on that same `[Future](./future.md)`.

1. **Keep Critical Sections Small:** Only keep the `[Future](./future.md)` result retrieval (the `Get()` call) on the critical path.
2. **Prefer Callbacks for UI:** For UI-related updates, consider if a Signal-based approach is more appropriate than blocking the main thread with `Get()`.
3. **Handle Exceptions/Errors:** Always check `IsValid()` before interacting with the `[Future](./future.md)` object to ensure the underlying resource was properly allocated or the task was successfully scheduled.

> **Note:** The `[Future](./future.md)` mechanism is intended for inter-thread communication. Using it within the same thread to wait for itself will result in a permanent deadlock; ensure the producer and consumer threads are distinct.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/future)
