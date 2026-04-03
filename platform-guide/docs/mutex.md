---
id: mutex
title: "Mutex"
sidebar_label: "Mutex"
---
## Introduction to Dali::[Mutex](./mutex.md)

`Dali::Mutex` is a synchronization primitive designed to provide mutual exclusion for shared resources within the DALi engine's multithreaded architecture. It is specifically optimized for scenarios where strict binary ownership of data is required between the main event thread and background worker threads, ensuring that critical sections are accessed by only one thread at a time.

You should use `Dali::Mutex` when protecting non-thread-safe resources or complex state updates that cannot be handled via lock-free mechanisms or the event-dispatching queue. Unlike generic [threading](./threading.md) primitives, `Dali::Mutex` integrates directly with the internal DALi [threading](./threading.md) model to maintain consistency across the engine's subsystems.

## Lifecycle and Memory Management

Proper management of `Dali::Mutex` is essential to prevent resource leaks and avoid undefined behavior during engine shutdown. Because `Dali::Mutex` manages the underlying OS synchronization primitive, its lifecycle should be strictly bound to the lifespan of the resource it protects.

### Instantiation and Ownership
`Dali::Mutex` should typically be held as a member variable of the class managing the shared resource. Because the class is not intended to be a base class, it uses a non-virtual destructor to ensure optimal performance when handling synchronization [events](./events.md).

> Warning: Always ensure that a `Mutex` instance outlives any `ScopedLock` instances that reference it; otherwise, the behavior of the `ScopedLock` destructor is undefined.

## Core API Reference

The `Dali::Mutex` API provides the essential building blocks for creating exclusive access zones. Its design follows move-semantics to ensure that thread synchronization objects can be safely managed within modern C++ containers or transferred between ownership contexts.

### Constructors and Assignment
The `Mutex()` constructor initializes the underlying OS-level mutex. The move constructor `Mutex(Mutex &&rhs)` and the move assignment operator `operator=(Mutex &&rhs)` allow for the transfer of ownership of a mutex [object](./object.md), which is useful when reconfiguring thread-local resource managers.

```cpp
#include <dali/devel-api/threading/mutex.h>

class ResourceManager {
public:
  ResourceManager() = default;
  
  // Move semantics supported
  ResourceManager(ResourceManager&& other) noexcept 
    : mMutex(std::move(other.mMutex)) {}

private:
  Dali::Mutex mMutex;
};
```

### Scoped Locking
While `Dali::[Mutex](./mutex.md)` provides the primitive, `Dali::[Mutex](./mutex.md)::ScopedLock` is the preferred RAII (Resource Acquisition Is Initialization) wrapper. It acquires the lock upon construction and releases it automatically upon destruction, guaranteeing that the mutex is unlocked even if an exception occurs.

```cpp
void UpdateSharedData(Dali::Mutex& mutex, int& sharedValue) {
  // Lock is acquired here
  Dali::Mutex::ScopedLock lock(mutex);
  
  sharedValue++;
  
  // Lock is automatically released when 'lock' goes out of scope
}
```

## Locking State and Diagnostics

Monitoring the state of a synchronization object is critical for debugging race conditions and verifying the integrity of complex, multi-threaded operations.

### Using IsLocked()
The `IsLocked()` method returns a boolean indicating whether the mutex is currently held by any thread. This is primarily intended for diagnostic purposes and assertions within the development phase.

> Note: `IsLocked()` should not be used as a replacement for proper locking logic (e.g., checking `IsLocked()` before performing an operation is susceptible to race conditions); always rely on `ScopedLock` for flow control.

```cpp
void DebugMutex(Dali::Mutex& mutex) {
  if (mutex.IsLocked()) {
    Dali::LOG_DEBUG("Mutex is currently held by another thread.\n");
  }
}
```

## Integration Patterns and Best Practices

To ensure high performance and engine stability, developers should follow established patterns when utilizing `Dali::[Mutex](./mutex.md)` within custom components or extension points.

### Preventing Deadlocks
When acquiring multiple mutexes, always ensure they are acquired in a consistent, global order across all threads. Avoid holding locks while calling external, unknown code, as this significantly increases the risk of circular waits.

### Minimizing Critical Section Duration
Keep the scope of `ScopedLock` as narrow as possible. Perform only the necessary state updates within the locked block, and offload time-consuming calculations or data processing to a thread-safe buffer before or after the lock duration.

### Integration with Engine Sibling Modules
When using `[Mutex](./mutex.md)` in conjunction with other threading components:
* For event-based cross-thread communication, utilize the task-dispatching mechanisms of the engine instead of direct mutex-based shared memory access. → See: [ThreadController]

```cpp
// Example of a thread-safe data getter
class ThreadSafeContainer {
public:
  int GetValue() {
    Dali::Mutex::ScopedLock lock(mMutex);
    return mValue;
  }

  void SetValue(int val) {
    Dali::Mutex::ScopedLock lock(mMutex);
    mValue = val;
  }

private:
  Dali::Mutex mMutex;
  int mValue{0};
};
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/mutex)
