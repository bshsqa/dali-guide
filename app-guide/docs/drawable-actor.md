---
id: drawable-actor
title: "DrawableActor"
sidebar_label: "DrawableActor"
---
## Understanding [DrawableActor](./drawable-actor.md)

The `DrawableActor` serves as the primary bridge for [rendering](./rendering.md) custom, callback-driven visual content within a `Dali::Ui::View` hierarchy. Unlike standard UI components that rely on predefined visual properties, `DrawableActor` allows developers to execute custom [rendering](./rendering.md) logic, making it the ideal tool for specialized graphics that fall outside the scope of standard UI widgets.

When you require direct control over the [rendering](./rendering.md) process or need to integrate procedural graphics that [update](./update.md) dynamically through a [rendering](./rendering.md) loop, `DrawableActor` is the correct choice. It is distinct because it offloads the drawing responsibility to a provided `RenderCallback`, enabling high-performance integration of low-level visual tasks within your application's `View` structure.

## Creating and Initializing

To include a `DrawableActor` in your interface, you must instantiate it using the factory method provided and nest it within a `Dali::Ui::View`. This initialization links your custom logic to the framework's [rendering](./rendering.md) pipeline.

### The New() Method

The `New()` method is the entry point for creating an instance of `DrawableActor`. It requires a `RenderCallback` [object](./object.md), which encapsulates the drawing instructions that the engine will execute.

*   **WHAT:** Creates a new `DrawableActor` instance associated with the provided callback.
*   **WHY:** You call this when you need to introduce a custom-rendered element into your UI scene.
*   **HOW:** Pass a reference to a `RenderCallback` [object](./object.md). The method returns a fully initialized `DrawableActor` handle ready to be added to a `Dali::Ui::View`.

```cpp
#include <dali/dali.h>
#include <dali/ui-toolkit/dali-ui-toolkit.h>

class MyCustomRenderer : public Dali::RenderCallback {
public:
    void Render() override {
        // Implement custom drawing logic here
    }
};

// Usage within a View context:
void SetupView(Dali::Ui::View& parentView) {
    MyCustomRenderer myRenderer;
    Dali::DrawableActor drawable = Dali::DrawableActor::New(myRenderer);
    
    // Add the drawable to the View hierarchy
    parentView.Add(drawable);
}
```

> Note: The `RenderCallback` lifecycle must outlive the `[DrawableActor](./drawable-actor.md)` instance, as the actor relies on the callback to perform its rendering operations.

## Configuring Visual Properties

`[DrawableActor](./drawable-actor.md)` inherits its spatial and structural properties from the base actor system, allowing it to function seamlessly within a `Dali::Ui::View`. While its visual content is determined by the callback, its placement and hierarchy are managed through standard parent-child relationships.

### Inheritance and Integration

`[DrawableActor](./drawable-actor.md)` integrates directly into the `Dali::Ui::View` transform chain. Once created, you treat it as a child of your `View`, inheriting transformations like position, scale, and orientation without extra configuration.

*   **HOW:** Use the `Add()` method on your `Dali::Ui::View` instance to attach the `[DrawableActor](./drawable-actor.md)`.
*   **SIDE EFFECTS:** By adding it to the `View`, the actor is subject to the `View`'s visibility and clipping constraints.

```cpp
Dali::Ui::View myView = Dali::Ui::View::New();
MyCustomRenderer renderer;
Dali::DrawableActor drawable = Dali::DrawableActor::New(renderer);

// Configure transform properties via the inherited Actor API
drawable.SetPosition(100.0f, 100.0f);
drawable.SetSize(200.0f, 200.0f);

// Attach to the view
myView.Add(drawable);
```

## Updating and Refreshing Content

Dynamic content within a `[DrawableActor](./drawable-actor.md)` is managed by updating the state of your `RenderCallback`. Because the `[DrawableActor](./drawable-actor.md)` invokes the callback during the render pass, changes to the data contained within the callback will be reflected in the next frame.

*   **BEST PRACTICE:** Avoid heavy calculations inside the `Render()` method. Update your state variables outside the render loop and use the `Render()` method only to submit drawing commands or draw-calls.

## Common Integration Patterns

A frequent pattern is to encapsulate a `[DrawableActor](./drawable-actor.md)` within a custom `Dali::Ui::View` class. This approach hides the complexity of the rendering logic from the main application flow.

```cpp
class CustomCanvasView : public Dali::Ui::View {
public:
    CustomCanvasView() {
        mDrawable = Dali::DrawableActor::New(mCallback);
        this->Add(mDrawable);
    }
private:
    MyCustomRenderer mCallback;
    Dali::DrawableActor mDrawable;
};
```

## Performance Best Practices

To ensure your application maintains a high frame rate while using `[DrawableActor](./drawable-actor.md)`, follow these constraints:

*   **Keep Callbacks Lean:** Since the `Render()` method runs on the render thread (or is tightly coupled to it), any blockages here will cause dropped frames across the entire application.
*   **Resource Management:** Ensure that any resources (such as textures or shaders) used within the `RenderCallback` are pre-allocated. Avoid allocation inside the `Render()` method.
*   **Use Appropriately:** If your visual requirement can be met using standard `Dali::Ui::View` properties or existing UI components, prefer those over `[DrawableActor](./drawable-actor.md)`. Use `[DrawableActor](./drawable-actor.md)` specifically for specialized, low-level [rendering](./rendering.md) requirements.

→ See: [Dali::Ui::View]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/drawable-actor)
