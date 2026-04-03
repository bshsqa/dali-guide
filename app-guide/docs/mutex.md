---
id: mutex
title: "Mutex"
sidebar_label: "Mutex"
---
## Introduction to Dali::[Mutex](./mutex.md)

`Dali::Mutex` is a synchronization primitive designed to enforce exclusive access to shared resources within a DALi application. Unlike higher-level thread abstractions, the [Mutex](./mutex.md) provides the foundational mechanism for preventing race conditions by ensuring that only one thread can execute a "critical section" of code at any given time.

You should use `Dali::Mutex` when your application logic involves multiple threads reading from and writing to the same shared data structures or application state. It is distinct from other [threading](./threading.md) primitives because it is lightweight, platform-agnostic within the DALi environment, and specifically optimized for the synchronization patterns required by the DALi thread model.

→ See: [Thread](threading_parent_page_link)

## Initializing a [Mutex](./mutex.md)

A `Dali::Mutex` is instantiated as a stack-allocated or member-variable [object](./object.md) that manages the underlying system synchronization primitives. Because the lifecycle of a mutex must span the duration of the shared resource's access, it is typically declared as a member variable within the class that manages the protected data.

### Constructing [Mutex](./mutex.md)
The constructor initializes a new, unlocked mutex instance ready for use. No arguments are required for the standard initialization.

```cpp
#include <dali/public-api/threading/mutex.h>

class MySharedResource {
public:
  MySharedResource() : mMutex() {}

private:
  Dali::Mutex mMutex;
  int mSharedData;
};
```

> Note: Ensure the `[Mutex](./mutex.md)` object remains in scope for as long as any thread attempts to lock it. Accessing a destroyed `[Mutex](./mutex.md)` is undefined behavior.

## Synchronizing Data Access

To protect shared resources, you must explicitly acquire a lock before accessing the data and release it immediately after the operation is complete. In the DALi public API, this is managed via the `Lock()` and `Unlock()` methods.

### Lock() and Unlock()
The `Lock()` method blocks the calling thread until the mutex is available, effectively granting the caller exclusive ownership. `Unlock()` releases the mutex, allowing other threads waiting on the same instance to proceed.

```cpp
void MySharedResource::UpdateData(int newValue) {
  // Acquire the lock to enter the critical section
  mMutex.Lock();
  
  // Critical section: modify shared resource
  mSharedData = newValue;
  
  // Release the lock to allow other threads access
  mMutex.Unlock();
}
```

> Warning: Always ensure that `Unlock()` is called on every code path that exits a critical section, including error handling blocks or exceptions, to prevent permanent deadlocks.

## Querying Lock State

The `IsLocked()` method allows for internal verification of the mutex state. This is primarily useful for assertions and debugging to ensure that a function is being called only when the expected synchronization state is active.

### IsLocked()
This method returns a boolean indicating whether the current mutex instance is currently held by any thread.

- **Returns**: `true` if the mutex is locked, `false` otherwise.

```cpp
void MySharedResource::ProcessData() {
  // Verification check to ensure thread safety during development
  if (mMutex.IsLocked()) {
     // Perform operations that assume ownership of the lock
  }
}
```

> Note: `IsLocked()` provides a snapshot of the mutex state. In highly concurrent environments, the state may change immediately after the check returns; use this primarily for diagnostic purposes or internal assertions.

## Best Practices for Thread Safety

Implementing robust multi-threading requires minimizing the duration of locked segments to prevent performance bottlenecks. Follow these principles to ensure stability within your DALi application.

1. **Minimize Critical Section Scope**: Keep the duration between `Lock()` and `Unlock()` as short as possible to reduce contention and allow other threads to execute efficiently.
2. **Avoid Nested Locks**: Acquiring multiple mutexes can lead to circular dependencies. If your logic requires multiple locks, always acquire them in a consistent, application-wide order to avoid deadlocks.
3. **Keep Operations Simple**: Perform non-thread-safe calculations outside of the locked section whenever possible, locking only during the final update to the shared resource.
4. **Use RAII Patterns**: While `Dali::[Mutex](./mutex.md)` provides direct `Lock/Unlock` access, wrapping these in local scope-based guards is highly recommended to ensure that `Unlock()` is called automatically when the guard goes out of scope.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/mutex)
