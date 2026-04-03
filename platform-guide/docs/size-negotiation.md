---
id: size-negotiation
title: "size-negotiation"
sidebar_label: "size-negotiation"
---
## Introduction to Size Negotiation

Size negotiation is the fundamental engine mechanism in DALi that determines the geometry of actors within a scene. It balances the desired dimensions requested by individual nodes against the constraints imposed by their parent containers to resolve the final rendered size.

You should use size negotiation when building dynamic UIs where component dimensions must adapt to content, screen orientation changes, or parent-child layout relationships. Unlike static positioning, size negotiation is distinct because it operates as an iterative feedback loop, allowing for sophisticated, responsive [layouts](./layouts.md) without manual coordinate calculations for every child element.

## The Relayout Lifecycle

The relayout lifecycle is a multi-pass process triggered when an actor's size or layout requirements change, forcing the engine to recalculate the hierarchy. This process propagates size requirements from children to parents, then computes the actual dimensions allowed, eventually applying them back to the actor tree.

The engine maintains "dirty" flags on the scene graph to identify which nodes require a re-negotiation. During the layout pass, the system traverses these flagged nodes to ensure that the final geometry is consistent across the entire tree, preventing layout jitter or visual artifacts during runtime updates.

## Working with Dali::RelayoutContainer

`Dali::RelayoutContainer` is a specialized interface defined in `relayout-container.h` that acts as a collector for pending size changes during the negotiation cycle. It ensures that actors requiring a size [update](./update.md) are processed efficiently within a single layout pass.

### Adding Actors to the Container

The `Add` method is the primary way to register an actor for a pending size [update](./update.md) within the current negotiation cycle.

*   **WHAT**: Registers a specific actor and its target size within the relayout container.
*   **WHY**: Developers use this when manually forcing or suggesting a size [update](./update.md) for an actor that participates in the automated layout system.
*   **HOW**:
    *   `actor` (const Actor &): The actor instance to be resized.
    *   `size` (const Vector2 &): The target size requested for the actor.
*   **Notes**: The `Add` method performs a check; it will only add the relayout information if it does not already exist in the container to prevent redundant calculations.

```cpp
#include <dali/public-api/dali-core.h>
#include <dali/devel-api/adaptor-framework/relayout-container.h>

void UpdateActorSize(Dali::Actor actor, Dali::Vector2 newSize)
{
    // Access or instantiate the container for the current pass
    Dali::RelayoutContainer container;
    
    // Add the actor and requested size to the negotiation queue
    container.Add(actor, newSize);
}
```

## Integration API and Threading Model

Size negotiation occurs primarily on the Main (Event) thread, where the scene graph hierarchy is managed. This ensures that layout calculations remain deterministic and thread-safe by avoiding race conditions between the application logic and the render thread.

The render thread consumes the results of these calculations, which are synchronized via internal engine message queues. While the `RelayoutContainer` handles the bookkeeping of size requests, developers should avoid performing heavy operations inside layout callbacks to prevent blocking the Main thread, which would directly impact frame latency.

## Constraint Resolution Mechanisms

The negotiation engine resolves conflicts by comparing the `SizeNegotiation` properties of an actor against the parent's layout requirements. When an actor requests a specific size, the container checks this against available constraints (like minimum/maximum size limits).

If a conflict arises, the engine prioritizes the parent's constraints, which act as the boundary condition for the child's requested size. The `RelayoutContainer` serves as the intermediary for these resolutions, capturing the state of the negotiation so that once the traversal is complete, the engine can commit the final, validated dimensions to the actor.

> Note: For complex dynamic layouts, consider using layout-group functionality to manage sets of children. → See: [LayoutGroup]

## Performance Optimization for Layouts

To minimize performance overhead, DALi utilizes a "dirty flag" system to ensure that only branches of the scene graph affected by size changes are traversed. Frequent layout updates can lead to redundant calculations; therefore, grouping multiple changes before triggering a relayout pass is best practice.

*   **Avoid Over-Invalidation**: Do not force relayouts on every frame if content is static.
*   **Container Efficiency**: Leverage the `RelayoutContainer` to batch additions, as the engine optimizes these calls to process the hierarchy in the fewest possible traversals.
*   **[Layout](./layout.md) Properties**: Prefer using established layout properties over custom scripts to allow the engine to cache layout computations effectively. 

> Warning: Triggering a layout pass from within a size negotiation callback can lead to infinite loops if not carefully guarded, as the relayout will mark the actor as dirty again.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/size-negotiation)
