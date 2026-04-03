---
id: drawable-actor
title: "DrawableActor"
sidebar_label: "DrawableActor"
---
## Introduction to [DrawableActor](./drawable-actor.md)

`DrawableActor` serves as the specialized bridge between the high-level `Dali::Ui::View` abstraction and the low-level [rendering](./rendering.md) pipeline. It is designed for scenarios where standard UI controls are insufficient and you require custom, low-level [rendering](./rendering.md) logic executed within the DALi engine's draw phase.

You should use `DrawableActor` when you need to perform frame-by-frame procedural drawing or integrate custom [rendering](./rendering.md) callbacks that must be synchronized with the DALi frame lifecycle. Unlike generic actors, this component is distinct because it specifically delegates its [rendering](./rendering.md) responsibility to a provided `RenderCallback` [object](./object.md), making it the primary mechanism for custom graphic injections.

## Lifecycle and Initialization

The `DrawableActor` follows the standard DALi handle-based memory management model, where instances are lightweight objects that point to an internal engine resource. Proper initialization is required to connect your [rendering](./rendering.md) logic to the DALi scene graph.

### The New Factory Method
The `New()` method is the primary entry point for instantiation, requiring a reference to an implementation of `RenderCallback`.

*   **WHAT:** Creates a new `DrawableActor` instance associated with your [rendering](./rendering.md) logic.
*   **WHY:** This is the only way to construct an [object](./object.md) that performs custom [rendering](./rendering.md) within the scene graph.
*   **HOW:** Pass a reference to a `RenderCallback` instance. The returned `DrawableActor` [object](./object.md) must be held as a member or managed by a container to ensure the lifecycle remains active while the actor is in the scene.
*   **CODE:**
```cpp
class MyRenderer : public Dali::RenderCallback {
public:
    void OnRender(Dali::RenderStatus& status) override {
        // Perform custom low-level rendering
    }
};

// Inside your initialization logic:
MyRenderer* myCallback = new MyRenderer();
Dali::DrawableActor drawable = Dali::DrawableActor::New(*myCallback);
```

> **Warning:** The `RenderCallback` object passed to `New()` must remain valid for the entire lifetime of the `[DrawableActor](./drawable-actor.md)`. Ensure your callback object outlives the actor to prevent null-pointer dereferences during the render pass.

## View Integration and Encapsulation

While `[DrawableActor](./drawable-actor.md)` provides the rendering capability, it should be encapsulated within a `Dali::Ui::View` to benefit from the broader UI framework features such as event handling, layout participation, and style management.

### Integrating with View
To bridge the gap, you add the `[DrawableActor](./drawable-actor.md)` as a child or a component within your `View` hierarchy.

*   **WHAT:** This pattern uses the `View` as the container for logic and the `[DrawableActor](./drawable-actor.md)` as the visual provider.
*   **HOW:** Use the `Add()` method inherited from the base actor structure to attach the drawable instance to your view.
*   **CODE:**
```cpp
void CreateMyCustomView(Dali::Ui::View& view) {
    auto renderer = std::make_unique<MyRenderer>();
    Dali::DrawableActor drawable = Dali::DrawableActor::New(*renderer);
    
    // Encapsulate the drawable within the view's hierarchy
    view.Add(drawable);
}
```

## Rendering Pipeline and Thread Safety

`[DrawableActor](./drawable-actor.md)` operates within the context of the DALi Render thread, which is separate from the application's main logic thread. Any updates to the state of your `RenderCallback` must be handled with extreme care to avoid race conditions.

### Thread Synchronization
When updating properties that influence your custom rendering, you must ensure that state changes are thread-safe. Avoid blocking the render thread, as this will directly result in dropped frames and stuttering in the UI.

> **Note:** The `OnRender` method is executed on the render thread. Avoid performing heavy computations, memory allocations, or network requests within this callback.

## Resource Management and Property Binding

Resource management in `[DrawableActor](./drawable-actor.md)` involves linking your rendering state to the DALi property system. While the `[DrawableActor](./drawable-actor.md)` handles the execution, you are responsible for the lifecycle of the graphical assets it uses.

### Performance Considerations
If your rendering logic requires external textures or buffer objects, ensure these are pre-allocated during the `View` initialization phase. Frequent allocation inside the render loop is a common cause of performance degradation.

## Best Practices for UI Performance

To maintain fluid frame rates, `[DrawableActor](./drawable-actor.md)` should be used sparingly and only when standard `View` components (like `[ImageView](./static-image-view.md)` or `TextLabel`) cannot achieve the desired effect.

1.  **Minimize Complexity:** Keep the `RenderCallback` code as lean as possible.
2.  **Culling:** Always ensure that your `[DrawableActor](./drawable-actor.md)` is either hidden or removed from the scene graph when it is outside the viewport, as the `RenderCallback` will continue to trigger if the actor is merely off-screen.
3.  **Avoid Overdraw:** Use clipping or visibility properties to prevent the renderer from processing parts of the screen that are obscured by other UI elements.
→ See: [Dali::Actor] for advanced scene-graph visibility and culling properties.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/drawable-actor)
