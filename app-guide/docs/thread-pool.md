---
id: thread-pool
title: "ThreadPool"
sidebar_label: "ThreadPool"
---
## Introduction to Thread Pool

The DALi `ThreadPool` component provides a managed set of worker threads designed to handle heavy computational tasks without blocking the main UI thread. Unlike single-threaded task dispatchers, the `ThreadPool` allows for parallel execution of multiple tasks, making it ideal for CPU-bound operations such as data processing, complex mathematical calculations, or file parsing.

Developers should choose `ThreadPool` when they have a queue of independent operations that can run concurrently. It is distinct because it maintains a persistent pool of threads, reducing the performance overhead associated with frequently creating and destroying individual threads.

## Initializing the Thread Pool

Initializing a `ThreadPool` involves defining the capacity of the worker group to match the hardware capabilities of the target device. Proper configuration ensures that your application maintains responsiveness without over-subscribing CPU resources.

### [ThreadPool](./thread-pool.md)(uint32_t threadCount)

The constructor initializes the pool with a fixed number of worker threads. You should determine the `threadCount` based on the expected workload intensity and the device's multi-core capabilities.

* **Parameters:**
    * `threadCount` (`uint32_t`): The number of threads to spawn and maintain within the pool.
* **Return:** An instance of `ThreadPool`.

```cpp
#include <dali/devel-api/threading/thread-pool.h>

// Create a pool with 4 worker threads
uint32_t workerThreads = 4;
auto myThreadPool = Dali::ThreadPool::New(workerThreads);
```

> Note: Creating too many threads can lead to excessive context switching, which may negatively impact UI performance. A value matching the number of available CPU cores is typically optimal.

## Submitting Tasks for Execution

Submitting tasks allows you to offload functions or lambdas to the background threads managed by the pool. The `[ThreadPool](./thread-pool.md)` handles the task scheduling internally, executing items as worker threads become available.

### void SubmitTask(CallbackBase* callback)

This method schedules a single task for execution. The task will be picked up by the next available worker thread.

* **Parameters:**
    * `callback` (`CallbackBase*`): A pointer to the callback object representing the task to be executed.
* **Side Effects:** The pool takes ownership of the callback; ensure the logic within the callback is thread-safe.

```cpp
// Example of submitting a background task
void MyWorkerFunction() 
{
  // Perform heavy computation here
}

auto callback = MakeCallback(&MyWorkerFunction);
myThreadPool->SubmitTask(callback);
```

## Managing Task Completion

In many scenarios, the main UI thread must wait for specific background operations to conclude before updating the application state. The `Wait` functionality provides a synchronization mechanism to ensure data integrity.

### void Wait()

The `Wait` function blocks the calling thread until all currently submitted tasks in the pool have completed execution.

* **Usage:** Use this carefully on the main thread, as calling `Wait()` will cause the UI to freeze until all background tasks finish.

```cpp
// Submit multiple tasks and wait for them to finish
myThreadPool->SubmitTask(MakeCallback(&TaskA));
myThreadPool->SubmitTask(MakeCallback(&TaskB));

// Synchronize with the background work
myThreadPool->Wait();
// Background work is now guaranteed to be complete
```

## Monitoring Pool Status

Monitoring the activity of the `[ThreadPool](./thread-pool.md)` helps in telemetry and performance tuning. You can inspect the current load to determine if you should adjust dynamic workloads.

### uint32_t GetThreadCount() const

Returns the total number of threads managed by the pool.

* **Return:** The `uint32_t` value representing the configured thread capacity.

```cpp
uint32_t count = myThreadPool->GetThreadCount();
Dali::LogInfo("ThreadPool initialized with %d threads\n", count);
```

## Best Practices for Threading

Effective use of the `[ThreadPool](./thread-pool.md)` requires strict adherence to thread-safety principles. Since tasks run outside the main thread, they cannot directly modify UI components or resources.

* **Avoid Main Thread Access:** Never access or modify `Dali::Actor` or other UI-related objects from within a task executed by the `[ThreadPool](./thread-pool.md)`. Use thread-safe signaling to communicate results back to the main thread.
* **Task Granularity:** Avoid submitting extremely short tasks, as the overhead of queueing can outweigh the benefits of parallelization.
* **Resource Contention:** If multiple tasks access the same shared memory, use mutexes or atomic variables to prevent race conditions.
* **Responsiveness:** Always consider the user experience; if a task must complete before the user can interact with the app, ensure a loading indicator is active while `Wait()` is invoked or tasks are pending.

→ See: [TaskQueue] for alternative, sequential task processing needs.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/thread-pool)
