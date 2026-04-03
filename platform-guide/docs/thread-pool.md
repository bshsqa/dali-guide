---
id: thread-pool
title: "ThreadPool"
sidebar_label: "ThreadPool"
---
## Introduction to [ThreadPool](./thread-pool.md)

The `Dali::ThreadPool` is a specialized concurrency [utility](./utility.md) designed to manage a set of worker threads, enabling the offloading of computationally expensive tasks from the DALi main event loop to background threads. Unlike simple thread creation which incurs high overhead for frequent operations, `ThreadPool` maintains a persistent set of worker threads that can be reused to process arbitrary tasks, making it ideal for background data processing, heavy image manipulation, or complex calculations that would otherwise cause frame drops in the GUI.

The `ThreadPool` is distinct in its ability to provide fine-grained control over task distribution via explicit indexing and bitmask-based affinity, allowing developers to ensure specific tasks are handled by deterministic threads.

## Initialization and Lifecycle Management

Proper initialization is required before task submission to ensure worker threads are allocated and ready for processing. The lifecycle is tied to the `ThreadPool` instance; once the [object](./object.md) is destroyed, the pool is shut down.

### Constructor and Initialization
The `ThreadPool` must first be instantiated and then explicitly initialized using `Initialize()`. The `threadCount` parameter determines the degree of parallelism.

- **`Initialize(uint32_t threadCount)`**: Prepares the internal worker thread array. If `threadCount` is `0`, the implementation typically defaults to a system-appropriate number of workers. Returns `true` on success.

```cpp
#include <dali/devel-api/threading/thread-pool.h>

void SetupPool() {
    Dali::ThreadPool pool;
    // Initialize with 4 worker threads
    if (pool.Initialize(4)) {
        // Pool is ready for tasks
    }
}
```

> Note: Attempting to submit tasks to an uninitialized `[ThreadPool](./thread-pool.md)` or before `Initialize` returns true will result in undefined behavior. Always verify the return value of `Initialize`.

## Task Submission Patterns

`[ThreadPool](./thread-pool.md)` provides flexible APIs for dispatching work, ranging from single task submission to bulk processing across specific thread groups.

### Submitting Tasks
- **`SubmitTask(uint32_t workerIndex, const Task &task)`**: Dispatches a single `Task` to a specific worker thread identified by `workerIndex`. It returns a `SharedFuture` which can be used to monitor task completion.
- **`SubmitTasks(const std::vector<Task> &tasks)`**: Distributes a collection of tasks across the available pool threads automatically.
- **`SubmitTasks(const std::vector<Task> &tasks, uint32_t threadMask)`**: Dispatches tasks only to threads included in the `threadMask` bitfield.

```cpp
// Example: Distributing tasks across a subset of workers
void DispatchWork(Dali::ThreadPool& pool) {
    std::vector<Dali::Task> tasks;
    // ... populate tasks ...

    // Submit tasks to threads 0 and 2 (binary 0101 = 5)
    uint32_t mask = 0x5; 
    auto futureGroup = pool.SubmitTasks(tasks, mask);
}
```

## Synchronous Waiting and Thread Blocking

When synchronization is required—such as ensuring a set of background calculations are complete before updating a UI component—the `Wait()` method serves as the primary barrier.

- **`Wait()`**: Blocks the calling thread until all currently queued tasks across all worker threads have reached the idle state.

```cpp
void ProcessAndSync(Dali::ThreadPool& pool) {
    // Submit tasks
    pool.SubmitTasks(myTasks);

    // Block until all work is finished
    pool.Wait();

    // Safe to update GUI or proceed with results
}
```

> Warning: Calling `Wait()` on the main event loop thread will freeze the DALi application GUI. Use this only when synchronous completion is mandatory for state consistency.

## Concurrency and Thread Safety Considerations

The `[ThreadPool](./thread-pool.md)` manages internal queues to prevent race conditions during task submission. However, users must manage memory safety for the data accessed within the `Task` objects themselves.

- **DALi Core Objects**: Do not modify UI objects (e.g., `Actor`, `View`) directly from within a background task submitted to the `[ThreadPool](./thread-pool.md)`. The DALi scene graph is not thread-safe.
- **State Protection**: Use mutexes or other synchronization primitives if tasks share state with other threads.

## Best Practices for Performance Tuning

Performance tuning in a `[ThreadPool](./thread-pool.md)` environment revolves around the `threadCount`.

- **`GetWorkerCount()`**: Returns the number of threads currently managed by the pool. 
- **Guidance**: Oversubscribing threads (creating more threads than physical CPU cores) can lead to excessive context switching, degrading performance. For CPU-bound tasks, set the `threadCount` to match the number of available logical cores. For I/O-bound tasks, a slightly higher thread count may improve throughput.

```cpp
void OptimizePool(Dali::ThreadPool& pool) {
    size_t count = pool.GetWorkerCount();
    // Dynamically adjust logic based on discovered worker count
}
```

→ See: [Thread/Task API](threading) for underlying thread synchronization primitives used by the pool.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/thread-pool)
