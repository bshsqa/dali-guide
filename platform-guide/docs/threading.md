---
id: threading
title: "threading"
sidebar_label: "threading"
---
## Introduction to DALi Threading

The DALi [threading](./threading.md) module provides a robust framework for managing asynchronous execution and cross-thread synchronization within the engine. It allows developers to offload intensive tasks, such as data processing or resource loading, to background threads while maintaining the responsiveness of the engine's main event loop.

By utilizing these primitives, developers can effectively manage resource contention and ensure that multi-threaded operations do not interfere with the frame-critical operations handled by the DALi core.

## Thread Safety and Concurrency Models

DALi maintains a strict concurrency model where most UI-related operations, particularly those involving `Dali::Actor` and its hierarchy, must occur on the main thread. Thread safety is achieved through a combination of explicit synchronization primitives and message passing to the main loop.

Memory visibility is managed by adhering to standard C++ memory ordering rules provided by the underlying synchronization primitives. When interacting with engine objects from background threads, developers should utilize the `Dali::EventThreadCallback` or equivalent mechanisms to bridge the gap between background workers and the UI thread.

## Internal Lifecycle and Integration

DALi threads are deeply integrated with the engine's internal lifecycle to prevent race conditions during scene graph traversal or property [animation](./animation.md). The engine's main loop acts as the primary orchestrator, ensuring that state changes initiated from background threads are serialized correctly before the next render pass.

When a background thread needs to communicate with the DALi core, it must ensure that any modifications to the scene graph—such as adding or removing children—are scheduled through the appropriate synchronization barriers to avoid violating the thread-affinity requirements of `Dali::Actor` objects.

## Sub-Components Overview

The [threading](./threading.md) module is comprised of several specialized primitives designed to address specific concurrency challenges:

* **[ConditionalWait](./conditional-wait.md)**: Provides a mechanism for threads to suspend execution until a specific state condition is met or a signal is received. → See: [conditional-wait]
* **[Future](./future.md)**: A handle for representing the eventual result of an asynchronous operation, allowing for non-blocking retrieval of background task completion. → See: [future]
* **[Mutex](./mutex.md)**: A fundamental mutual exclusion primitive used to protect shared data structures from concurrent access. → See: [mutex]
* **[ThreadPool](./thread-pool.md)**: Manages a collection of worker threads to distribute concurrent tasks, optimizing resource utilization by preventing excessive thread creation overhead. → See: [thread-pool]

## Developer Best Practices

Efficient multi-[threading](./threading.md) in DALi requires careful consideration of lock contention and the prevention of deadlocks. Always prefer finer-grained locking strategies and utilize the RAII-based `ScopedLock` patterns provided in the `devel-api` to ensure that mutexes are released correctly even in the event of exceptions.

> Warning: Never attempt to instantiate or modify `Dali::Actor` objects directly from a background thread. Doing so will lead to undefined behavior, as the engine's internal state is not thread-safe regarding scene graph manipulation. Always dispatch these tasks to the main thread.

### Managing Thread-Safe Resource Access

When working with shared state that is not managed by the engine, always encapsulate access behind a `Mutex`. This prevents data races when multiple threads attempt to read or write to non-atomic memory locations.

```cpp
#include <dali/devel-api/threading/mutex.h>
#include <dali/devel-api/threading/scoped-lock.h>

// Example: Protecting a shared resource
class ResourceContainer {
public:
  void UpdateValue(int newValue) {
    Dali::Mutex::ScopedLock lock(mMutex);
    mValue = newValue;
  }

private:
  Dali::Mutex mMutex;
  int mValue{0};
};
```

By strictly adhering to these concurrency patterns, developers can leverage the full power of multi-core processors while maintaining the stability and performance expected of a high-performance GUI framework.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/threading)
