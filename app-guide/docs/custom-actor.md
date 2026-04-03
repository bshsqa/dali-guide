---
id: custom-actor
title: "CustomActor"
sidebar_label: "CustomActor"
---
## Introduction to Custom Actors

Custom actors are used when you need to encapsulate complex UI behavior, custom [rendering](./rendering.md) logic, or specialized event handling that cannot be achieved through the standard composition of existing `Dali::Ui::View` components. By deriving from `Dali::CustomActorImpl`, you gain fine-grained control over the actor's lifecycle, layout negotiations, and property updates, allowing you to create high-performance, reusable UI components.

While `Dali::Ui::View` is the standard for general UI composition, `CustomActor` is intended for developers building low-level controls or system-level components. It bridges the gap between raw DALi engine nodes and high-level application Views, providing an abstraction point for complex internal state.

## Defining Your Custom Component

To create a custom component, you must implement a class derived from `Dali::CustomActorImpl` and wrap it in a `Dali::CustomActor` handle. This structure separates your internal business logic (in the `Impl` class) from the public UI handle used by the rest of the application.

### Implementing the Custom Logic
Your implementation class must override several virtual methods to handle lifecycle [events](./events.md) like scene connection and size changes.

```cpp
#include <dali/dali.h>

class MyCustomControl : public Dali::CustomActorImpl
{
public:
  MyCustomControl() : CustomActorImpl(Dali::CustomActorImpl::ACTOR_FLAG_COUNT) {}

  void OnSceneConnection(int32_t depth) override { /* Initialization logic */ }
  void OnSceneDisconnection() override { /* Cleanup logic */ }
  void OnSizeSet(const Dali::Vector3& targetSize) override { /* React to size changes */ }
  void OnRelayout(const Dali::Vector2& size, Dali::RelayoutContainer& container) override { /* Custom layout logic */ }
  // ... implement other required virtual methods
};
```

## Managing Custom Implementation Logic

The `GetImplementation` method provides access to the internal `CustomActorImpl` object from a `[CustomActor](./custom-actor.md)` handle. This is essential when you need to update internal state or trigger internal methods from outside the custom component.

### Accessing the Impl
Use `GetImplementation` to reach your custom logic after retrieving an actor instance from the scene tree.

```cpp
// Assuming 'myActor' is a CustomActor instance
if (myActor)
{
  Dali::CustomActorImpl& impl = myActor.GetImplementation();
  // Access custom methods or properties on your implementation
  impl.RelayoutRequest();
}
```

> Note: `GetImplementation` returns a reference to the underlying implementation. Ensure the handle is valid before calling this to avoid undefined behavior.

## Integrating with the View Lifecycle

While `[CustomActor](./custom-actor.md)` manages the low-level rendering and lifecycle, you typically associate these with a `Dali::Ui::View` to integrate them into your application's UI hierarchy.

### Initialization and Registration
You must instantiate your `[CustomActor](./custom-actor.md)` using your implementation instance. The `Self()` method inside the `CustomActorImpl` allows the implementation to return its own public-facing handle.

```cpp
// Inside your implementation class
Dali::CustomActor MyCustomControl::GetSelf() 
{
  return Self(); 
}

// In your application code:
MyCustomControl* impl = new MyCustomControl();
Dali::CustomActor customActor = Dali::CustomActor(*impl);

// Add to your application View
Dali::Ui::View myView = Dali::Ui::View::New();
myView.Add(customActor);
```

## Safe Casting and Type Handling

Because `[CustomActor](./custom-actor.md)` is a generic handle, you often need to verify that a generic `Actor` (or handle) is indeed your specific `[CustomActor](./custom-actor.md)` type. 

### Using DownCast
The `DownCast` method allows you to safely convert a `BaseHandle` or generic actor handle to a `Dali::[CustomActor](./custom-actor.md)`. This ensures type safety when traversing the scene graph.

```cpp
Dali::BaseHandle handle = GetHandleFromSomewhere();
Dali::CustomActor custom = Dali::CustomActor::DownCast(handle);

if (custom)
{
  // It is safe to use the handle
}
```

## Best Practices for Custom Actors

When developing custom components, maintain a strict separation between your `Impl` class (which manages state and geometry) and the public-facing API.

*   **Performance:** Override `OnRelayout` sparingly. Excessive logic inside relayout hooks can impact frame rates during window resizing or layout updates.
*   **Encapsulation:** Keep your implementation logic strictly inside `CustomActorImpl`. Expose functionality to the application level through methods on the `[CustomActor](./custom-actor.md)` handle, not by reaching into the implementation object whenever possible.
*   **Scene Cleanup:** Always perform heavy cleanup in `OnSceneDisconnection` to prevent memory leaks or lingering references to engine resources.
*   **Size Negotiation:** Use `RelayoutRequest()` when your component's internal state changes in a way that requires the parent `View` to recalculate the size of your component.

> Warning: Do not manipulate the parent-child hierarchy directly inside the `OnChildAdd` or `OnChildRemove` callbacks to avoid recursive calls and stack overflows.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/custom-actor)
