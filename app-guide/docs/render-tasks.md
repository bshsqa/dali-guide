---
id: render-tasks
title: "render-tasks"
sidebar_label: "render-tasks"
---
## Introduction to Render Tasks

Render Tasks in the DALi framework provide the mechanism for defining how your application's scene graph is traversed and rendered to a surface. They act as the bridge between your logical scene structure and the final visual output displayed to the user.

You should use render tasks when you need granular control over the [rendering](./rendering.md) pipeline, such as performing off-screen [rendering](./rendering.md), creating multiple viewports, or controlling the specific order in which different parts of your scene are drawn. Unlike standard automatic [rendering](./rendering.md), render tasks offer a distinct, configurable approach to composition, allowing for complex visual effects and multi-pass [rendering](./rendering.md) scenarios.

## Managing the Render Task List

The render task list is the global container that dictates the sequence of [rendering](./rendering.md) operations. By manipulating this list, you control the order in which specific viewports or surfaces are updated.

### Adding and Removing Render Tasks
To influence the [rendering](./rendering.md) sequence, you must interact with the `RenderTaskList`. You can acquire the list from the `Stage` and append, insert, or remove tasks as required by your application's state.

> Note: The [rendering](./rendering.md) sequence is determined by the order of tasks in the list; items at the beginning are rendered first.

```cpp
// Example: Adding a new render task to the end of the global list
Dali::RenderTaskList taskList = Dali::Stage::GetCurrent().GetRenderTaskList();
Dali::RenderTask newTask = taskList.CreateTask();
// The task is now part of the rendering pipeline
```

## Configuring Render Task Properties

Render tasks use a property-based system to define their behavioral characteristics, such as viewport boundaries, clipping regions, and target cameras. These properties ensure that a render task knows exactly what to render and where on the screen it should appear.

### Defining Viewport and Camera Settings
Each render task can be configured with specific properties to isolate a segment of the scene. You can assign a specific `[CameraActor](./camera-actor.md)` to a task to change the perspective for that specific render pass.

> Note: Ensure your properties are set correctly to avoid rendering artifacts, especially when defining custom viewports.

```cpp
// Example: Setting a camera on a render task
Dali::RenderTask task = taskList.CreateTask();
Dali::CameraActor camera = Dali::CameraActor::New();
task.SetCameraActor(camera);

// Setting a custom viewport rectangle
task.SetViewportPosition(Dali::Vector2(0.0f, 0.0f));
task.SetViewportSize(Dali::Vector2(100.0f, 100.0f));
```

## Off-screen Rendering and Framebuffer Targets

Off-screen rendering is achieved by assigning a `[FrameBuffer](./frame-buffer.md)` to a render task. This directs the output of the task to an off-screen texture rather than the primary display surface.

### Rendering to a Framebuffer
By providing a frame buffer target, you can capture the visual output of an entire sub-tree of the scene graph. This is highly effective for creating UI effects like blurred backgrounds, reflections, or complex compositing layers.

> Warning: Off-screen rendering involves additional GPU overhead. Use it sparingly to maintain optimal frame rates.

```cpp
// Example: Assigning a framebuffer to a task
Dali::FrameBuffer frameBuffer = Dali::FrameBuffer::New(width, height, Dali::FrameBuffer::COLOR);
Dali::RenderTask task = taskList.CreateTask();
task.SetTargetFrameBuffer(frameBuffer);
```

## Controlling Render Order and Visibility

The render task list maintains an explicit order. By controlling this order, you can ensure that certain elements (like background overlays or debug HUDs) are rendered either before or after the main scene content.

### Prioritizing Tasks
You can adjust the order of execution by moving tasks within the `RenderTaskList`. A task that appears later in the list will be drawn on top of tasks that appeared earlier.

```cpp
// Example: Moving a task to the front of the list to ensure it renders first
Dali::RenderTaskList taskList = Dali::Stage::GetCurrent().GetRenderTaskList();
taskList.RemoveTask(myTask);
taskList.PushFront(myTask);
```

## Advanced Camera and Viewport Management

Advanced rendering layouts, such as split-screen views or picture-in-picture displays, are created by defining multiple render tasks, each with its own `[CameraActor](./camera-actor.md)` and unique viewport rectangle.

### Split-Screen and Custom Views
By creating two separate tasks and assigning different camera properties and screen-space coordinates, you can effectively segment the display. Each task operates independently, allowing for complex multi-view setups.

> Note: If you require complex coordinate transformations beyond standard viewport settings, this is a platform-level detail — refer to the platform guide for advanced projection matrix overrides.

```cpp
// Example: Creating a split-screen effect
Dali::RenderTaskList taskList = Dali::Stage::GetCurrent().GetRenderTaskList();

// Left half
Dali::RenderTask leftTask = taskList.CreateTask();
leftTask.SetViewportPosition(Dali::Vector2(0.0f, 0.0f));
leftTask.SetViewportSize(Dali::Vector2(screenWidth / 2, screenHeight));

// Right half
Dali::RenderTask rightTask = taskList.CreateTask();
rightTask.SetViewportPosition(Dali::Vector2(screenWidth / 2, 0.0f));
rightTask.SetViewportSize(Dali::Vector2(screenWidth / 2, screenHeight));
```

→ See: [[Layer](./layer.md)] for managing scene depth within a single render task.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/render-tasks)
