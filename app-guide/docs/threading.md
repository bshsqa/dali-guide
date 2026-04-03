---
id: threading
title: "threading"
sidebar_label: "threading"
---
## Introduction to DALi Threading

Threading in DALi is the mechanism that enables developers to offload intensive computational tasks from the main application thread to background workers. By decoupling heavy data processing from the UI event loop, you ensure the application remains responsive, avoiding frame drops and interface freezes.

Threading is distinct because DALi utilizes a thread-safe architecture where the main event loop handles all UI-related updates, while worker threads manage asynchronous data preparation. This separation necessitates explicit synchronization strategies to safely pass data back to the UI when background operations complete.

## Threading Best Practices

Effectively utilizing [threading](./threading.md) requires a strict separation of concerns between UI-bound logic and background tasks. The primary rule is that all manipulations of visual elements—such as modifying `Dali::Actor` properties—must occur on the main thread.

> Note: Accessing or modifying `Dali::Actor` objects directly from a secondary thread is strictly prohibited and leads to undefined behavior. Use synchronization primitives to ensure data is safely communicated to the main thread's event queue before updating the UI.

## Sub-Components Overview

The [threading](./threading.md) module provides a suite of tools to manage task execution and data integrity across concurrent threads.

*   **[Mutex](./mutex.md)**: A synchronization primitive that prevents multiple threads from accessing shared resources simultaneously. → See: [[Mutex](./mutex.md)]
*   **Conditional Wait**: A mechanism allowing threads to wait for specific conditions to be met before resuming execution. → See: [[ConditionalWait](./conditional-wait.md)]
*   **[Future](./future.md)**: A container for a value that may not yet be available, facilitating asynchronous result retrieval. → See: [[Future](./future.md)]
*   **Thread Pool**: An execution manager that maintains a set of worker threads to process tasks, optimizing system resource usage. → See: [[ThreadPool](./thread-pool.md)]

## Task Synchronization and Resource Protection

Synchronization primitives ensure that your application maintains data integrity when multiple threads attempt to read or write to shared variables. These tools are the foundation of safe multi-threaded communication.

### [Mutex](./mutex.md)
A `Mutex` (Mutual Exclusion) ensures that only one thread can access a critical section of code at a time. This is essential when updating shared state or accessing resources that are not thread-safe.

*   **Usage**: Call `Lock()` before accessing shared data and `Unlock()` immediately after the operation is complete to avoid deadlocks. 
*   **Platform Note**: Advanced scoped locking (e.g., `Mutex::ScopedLock`) is a platform-level detail; please refer to the platform guide for specific RAII implementations.

```cpp
#include <dali/dali.h>

// Example: Protecting a shared counter
Dali::Mutex myMutex;
int sharedCounter = 0;

void UpdateCounter() {
    myMutex.Lock();
    sharedCounter++;
    myMutex.Unlock();
}
```

## Asynchronous Task Management

Asynchronous management allows your application to execute long-running operations, such as file I/O or complex mathematical calculations, without stalling the visual interface.

### Future
A `[Future](./future.md)` allows you to trigger a task and collect the result once it has been computed. This pattern is ideal for non-blocking requests where the UI needs the result only after the work is finalized.

*   **What**: The `[Future](./future.md)` acts as a placeholder for a return value of an asynchronous operation.
*   **Why**: Use this to avoid blocking the main thread while waiting for slow data operations to complete.
*   **How**: The implementation logic typically involves a background thread updating the value contained within the future, which the main thread can query.

> Warning: Always ensure that the object being tracked by a `[Future](./future.md)` remains in scope for the duration of the background task to prevent memory access violations.

### ThreadPool
A `[ThreadPool](./thread-pool.md)` provides an abstraction for managing a collection of threads, allowing you to queue tasks for execution as resources become available. This prevents the performance overhead associated with creating and destroying threads manually for every small operation.

```cpp
// Logic flow for a worker task execution
void PerformHeavyCalculation(int input) {
    // 1. Queue a task into the ThreadPool
    // 2. The task runs on a worker thread
    // 3. Once finished, the task signals the main loop
    // 4. Use Actor::Add() or property updates inside a post-to-main-thread callback
}
```

> Note: While the `[ThreadPool](./thread-pool.md)` manages execution, remember that any modification to an `Actor` (like adding a child or changing a transform) must be routed back to the main thread before execution, as DALi components are not inherently thread-safe.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/threading)
