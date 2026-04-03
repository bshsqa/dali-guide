---
id: render-tasks
title: "render-tasks"
sidebar_label: "render-tasks"
---
## Introduction to Render Tasks

Render Tasks serve as the primary mechanism in DALi for defining off-screen [rendering](./rendering.md), frame buffer composition, and multi-pass [rendering](./rendering.md) pipelines. They provide the necessary abstraction to control how a specific portion of the scene graph is rendered, where it is directed (e.g., to an off-screen buffer vs. the screen), and which camera is used to capture the view.

By leveraging Render Tasks, developers can create complex visual effects such as reflections, mirrors, or post-processing passes. Unlike standard [rendering](./rendering.md), which is managed automatically by the DALi scene graph, Render Tasks allow for explicit orchestration of the [rendering](./rendering.md) pipeline, making them distinct for developers who need fine-grained control over frame composition.

## Render Task Lifecycle and Execution

The Render Task lifecycle begins upon creation and registration with the `RenderTaskList`, which manages the sequence of operations performed during the core render loop. Once a task is added to the list, the engine schedules its execution based on its relative priority and the configuration of its frame buffer and viewport.

### Lifecycle Phases
- **Initialization**: A `RenderTask` is instantiated, representing a discrete unit of [rendering](./rendering.md) work.
- **Attachment**: The task is added to the `RenderTaskList` associated with the `Stage`.
- **Execution**: During the frame [rendering](./rendering.md) traversal, the engine iterates through the `RenderTaskList`, executing tasks in order. 
- **Destruction**: Tasks are automatically cleaned up when removed from the list or when the associated scene graph objects are destroyed.

## Managing Render Task Collections

The `RenderTaskList` acts as a container for all active render tasks, allowing developers to manage the order and execution priority of [rendering](./rendering.md) passes. It ensures that tasks are processed according to the sequence required to correctly composite the final frame.

## Configuring Render Task Properties

Render Tasks are configured via the `Dali::RenderTask::Property` interface, which allows for dynamic adjustment of how a task interacts with the scene. Developers typically modify properties such as the `viewport`, the `camera`, and the `targetFrameBuffer` to define the spatial and visual parameters of the render pass.

> Note: Manipulating these properties directly affects the output of the task in the next render cycle. Ensure that properties are updated before the sync point to avoid flickering or visual artifacts.

```cpp
// Example: Configuring a simple Render Task
Dali::RenderTaskList taskList = Dali::Stage::GetCurrent().GetRenderTaskList();
Dali::RenderTask task = taskList.CreateTask();

// Configuring properties using the provided Property system
task.SetProperty(Dali::RenderTask::Property::REFRESH_RATE, 1);
task.SetProperty(Dali::RenderTask::Property::CLEAR_ENABLED, true);
```

## Thread Safety and Integration Guidelines

The `RenderTaskList` and individual `RenderTask` objects operate within the context of the DALi thread model, primarily interacting with the render thread. Developers must ensure that modifications to render tasks are performed on the main application thread, with the engine handling synchronization internally via the message queue.

> Warning: Directly modifying frame buffers or camera properties while a render pass is in progress can lead to race conditions. Always queue property changes through the standard property system to ensure thread safety.

→ See: [RenderTaskList]

## Internal Integration API Usage

Low-level custom rendering hooks can be implemented by utilizing the `Devel` and `Integration` namespaces within `render-task.cpp`. These provide access to advanced frame buffer attachments and custom shader pass injection that are not available in the standard Public API. 

When using `Integration` APIs, ensure that your implementation respects the engine’s internal state machine, particularly regarding clear color, depth testing, and stencil states, as these are globally shared across passes.

## Rendering Pipeline Performance Considerations

Every added `RenderTask` incurs a performance cost due to the potential for state changes, frame buffer swaps, and command buffer regeneration. To optimize complex multi-task render chains:
1. **Minimize Buffer Switches**: Reusing existing frame buffers across tasks reduces memory overhead and allocation latency.
2. **Task Culling**: Only enable tasks that are currently contributing to the visible output.
3. **Viewport Precision**: Ensure that viewports are tightly cropped to the actual area being updated to reduce the pixel fill rate requirement.

> Note: Excessive use of off-screen [rendering](./rendering.md) via Render Tasks can lead to significant memory pressure. Always profile the application to monitor the impact of additional render passes on the GPU's memory footprint.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/render-tasks)
