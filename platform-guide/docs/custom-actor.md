---
id: custom-actor
title: "CustomActor"
sidebar_label: "CustomActor"
---
## [CustomActor](./custom-actor.md) Fundamentals

The `CustomActor` component provides a low-level extension mechanism for the DALi engine, allowing developers to create bespoke UI elements with specialized lifecycle, layout, and [rendering](./rendering.md) behaviors that standard `Dali::Ui::View` objects do not natively support. While `View` is designed for general-purpose application composition, `CustomActor` is intended for developers building high-performance, custom control libraries or specialized graphical primitives where full control over the `OnRelayout`, `OnSceneConnection`, and [rendering](./rendering.md) pipeline hooks is required.

By inheriting from `CustomActorImpl`, you gain the ability to intercept internal engine [events](./events.md), such as size changes, child management notifications, and deep integration with the off-screen [rendering](./rendering.md) task system.

## Architecture and Implementation Pattern

DALi utilizes a bridge pattern to separate the public-facing handle (`Dali::CustomActor`) from the internal implementation (`Dali::CustomActorImpl`). This ensures that the engine can manage the [object](./object.md)'s lifecycle and memory while the developer focuses on defining custom behavior within the `Impl` class.

### The Handle-Implementation Bridge
To create a custom component, you must define a class inheriting from `CustomActorImpl` and associate it with a `CustomActor` handle.

```cpp
class MyCustomControlImpl : public Dali::CustomActorImpl
{
public:
  MyCustomControlImpl() : CustomActorImpl(0) {}
  
  // Custom logic goes here
};

// Usage
MyCustomControlImpl* impl = new MyCustomControlImpl();
Dali::CustomActor myActor( *impl );
```

> Note: Always manage the lifecycle of your `CustomActorImpl` through the engine’s ownership model. Once passed to the `[CustomActor](./custom-actor.md)` constructor, the handle effectively manages the reference to the implementation.

## Lifecycle and State Management

Managing the lifecycle of a `[CustomActor](./custom-actor.md)` requires deep awareness of its connection state to the scene graph and its internal property system. The `CustomActorImpl` provides various virtual hooks that the engine calls automatically when the actor state transitions.

### Hooking into Lifecycle Events
The following methods are essential for initializing resources or responding to the structural changes of your component:

*   **OnSceneConnection(int32_t depth)**: Invoked when the actor is added to the active scene graph.
*   **OnSceneDisconnection()**: Invoked when the actor is removed from the scene.
*   **OnChildAdd(Actor &child)** / **OnChildRemove(Actor &child)**: Notifies the implementation when the node hierarchy changes, allowing for dynamic re-layout or resource cleanup.

```cpp
void MyCustomControlImpl::OnSceneConnection(int32_t depth)
{
    // Initialize expensive resources or bind properties here
}

void MyCustomControlImpl::OnChildAdd(Dali::Actor &child)
{
    // React to structural changes, e.g., triggering a relayout
    RelayoutRequest();
}
```

### Accessing the Implementation
If you have a `Dali::[CustomActor](./custom-actor.md)` handle and need to access its internal logic, use the `GetImplementation()` method or `DownCast` to safely retrieve your concrete class.

```cpp
Dali::CustomActor actor = ...; // Existing handle
MyCustomControlImpl& impl = static_cast<MyCustomControlImpl&>(actor.GetImplementation());
```

## Custom Rendering and Scene Graph Integration

`[CustomActor](./custom-actor.md)` allows for advanced scenarios, such as off-screen rendering, which are vital for complex graphical components. These are managed via the `OffScreenRenderable` system.

### Off-Screen Rendering Tasks
If your component requires rendering to a buffer before being displayed, use the off-screen API. This is common for visual effects like blur or complex reflections.

*   **GetOffScreenRenderTasks**: Populates a list of rendering tasks associated with the actor.
*   **RequestRenderTaskReorder**: Tells the engine to re-evaluate the draw order when off-screen properties change.

```cpp
void MyCustomControlImpl::GetOffScreenRenderTasks(Dali::Vector<Dali::RenderTask>& tasks, bool isForward)
{
    // Retrieve internal tasks to define custom rendering order or targets
}
```

## Bridging CustomActor to View

While `[CustomActor](./custom-actor.md)` is low-level, you can wrap it inside a `Dali::Ui::View` to provide a standard interface for application developers. This allows you to expose `[CustomActor](./custom-actor.md)` features while maintaining the developer-friendly API surface of the `View` class.

### Encapsulation Pattern
Encapsulate the `[CustomActor](./custom-actor.md)` within a `View` structure to ensure that standard event listeners and property systems function as expected by end-users.

```cpp
class MyComponent : public Dali::Ui::View
{
public:
    MyComponent()
    {
        // Define your custom implementation
        auto impl = new MyCustomControlImpl();
        // Construct the view using the custom actor handle
        Dali::CustomActor actor(*impl);
        this->Add(actor);
    }
};
```
→ See: [Dali::Ui::View](https://docs.tizen.org) (for View-level API details).

## Performance and Threading Considerations

`[CustomActor](./custom-actor.md)` operates within the DALi event thread. All interactions with the engine's scene graph, including `RelayoutRequest` or property modifications, must occur on this thread.

### Best Practices
*   **Relayout Efficiency**: Calling `RelayoutRequest` triggers a full negotiation pass. Avoid calling this inside `OnRelayout` to prevent infinite layout loops.
*   **Property Sets**: Use `OnPropertySet` to update internal states rather than polling properties, as this allows the engine to optimize state changes.
*   **Memory Management**: Always override the virtual destructor `~CustomActorImpl()` to ensure clean teardown of resources when the actor is destroyed by the engine.

```cpp
void MyCustomControlImpl::OnSizeSet(const Dali::Vector3& targetSize)
{
    // Update internal layout logic based on new size
    // Do not call SetSize() here as it leads to recursion
}
```

> Warning: Performing heavy computational tasks inside `OnRelayout` or `OnSceneConnection` will block the main UI thread, resulting in dropped frames. Offload data processing to worker threads and only update the `[CustomActor](./custom-actor.md)` properties on the event thread using safe dispatch mechanisms.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/custom-actor)
