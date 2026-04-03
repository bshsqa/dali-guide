---
id: size-negotiation
title: "size-negotiation"
sidebar_label: "size-negotiation"
---
## Introduction to Size Negotiation

Size negotiation is the core mechanism in DALi that automates the calculation of actor dimensions and positions within the scene graph. By allowing parent actors to query their children for their preferred sizes, DALi facilitates the creation of responsive, dynamic UIs that automatically adapt to content changes and varying screen resolutions.

This system is distinct because it moves away from rigid, hard-coded coordinate positioning in favor of a declarative approach. You should use size negotiation whenever your application requires a flexible layout that reacts gracefully to content updates, font scaling, or device orientation changes, ensuring your UI remains consistent without manual coordinate recalculations.

## Understanding the RelayoutContainer

The `Dali::RelayoutContainer` is the primary interface used to aggregate and store the dimensions requested by actors during a layout pass. It acts as a collector, ensuring that when the engine processes a relayout, it has a centralized [object](./object.md) containing the necessary size data for all affected actors.

### Using RelayoutContainer::Add

The `Add` method is the fundamental way to register an actor's new size requirements during the negotiation phase.

*   **WHAT:** This method queues a specific `Actor` to be resized to the provided `Vector2` dimensions.
*   **WHY:** You use this when implementing custom layout logic where you have calculated the desired size for a child actor and need to commit that size to the engine's layout queue.
*   **HOW:** 
    *   `actor` (const Actor&): The actor whose size is being negotiated.
    *   `size` (const Vector2&): The target size that the actor should adopt.
    *   The method has no return value but updates the internal state of the container.

```cpp
#include <dali/dali.h>
#include <dali/public-api/object/relayout-container.h>

void UpdateLayout(Dali::RelayoutContainer& container, Dali::Actor& myActor)
{
    // Define the new desired size
    Dali::Vector2 newSize(200.0f, 100.0f);

    // Add the actor and its size to the container for processing
    container.Add(myActor, newSize);
}
```

> Note: `RelayoutContainer` is designed for use within the internal layout lifecycle. Ensure you are providing valid references to existing actors to prevent undefined behavior during the layout pass.

## Size Negotiation Flow

Size negotiation in DALi follows a multi-stage lifecycle that ensures consistency across the UI tree. When a change occurs (such as adding a child or changing a property), the system triggers a request that flows from the parent down to the children (size request) and then resolves back up the tree (layout application).

The `RelayoutContainer` serves as the transit mechanism during this flow. While the engine handles the orchestration of the layout pass, developers utilize the `RelayoutContainer` to provide the final sizing constraints that the engine then applies to the actors in the scene.

## Configuring Layout Requirements

Defining layout requirements involves specifying how an actor should behave under various constraints. This is achieved through the properties set on an actor, which the layout system then interprets during the negotiation flow.

> Note: While the `RelayoutContainer` manages the transmission of size data, the constraints themselves (like `SizeScalePolicy` or `MinimumSize`) are set via the actor's property system, which is a platform-level detail. Please refer to the platform guide for specific actor property keys.

→ See: [Actor]

## Debugging Layout Cycles

Layout cycles occur when an actor's size request triggers a chain of events that leads back to its own initial size request, creating an infinite loop. This typically happens if your custom layout logic creates a dependency where an actor's size is calculated based on its own output size.

To resolve these:
1.  Ensure that size requests are unidirectional (Parent -> Child).
2.  Avoid logic that causes a layout request to be triggered inside an observer of a layout change.
3.  Use the provided `RelayoutContainer` to batch updates, which helps the engine optimize and detect circular dependencies.

## Performance Optimization for Layouts

Optimizing layout performance is critical to maintaining a constant 60 or 120 FPS. Because size negotiation can trigger recursive traversals of the actor tree, minimizing the frequency and scope of these updates is essential for smooth performance.

*   **Batching:** Always group your layout updates using the `RelayoutContainer` whenever possible.
*   **Scope:** Limit the number of actors involved in a relayout request. Avoid triggering a full scene relayout if only a specific branch of the UI tree has changed.
*   **Consistency:** Avoid complex, multi-pass calculations in your layout logic that force the engine to reconcile conflicting size requirements repeatedly.

> Warning: Excessive layout passes triggered in every frame will degrade application performance significantly. Only trigger a relayout when a state change genuinely necessitates a geometry [update](./update.md).

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/size-negotiation)
