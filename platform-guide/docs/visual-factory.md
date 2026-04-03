---
id: visual-factory
title: "visual-factory"
sidebar_label: "visual-factory"
---
## Introduction to Visual Factory

The `VisualFactory` serves as the centralized service responsible for the creation, lifecycle management, and resource optimization of visual assets within the DALi GUI framework. It abstracts the complexities of graphics pipeline interaction, ensuring that visual components are instantiated efficiently and shared across the application where possible.

Developers should utilize the `VisualFactory` when building dynamic user interfaces that require high-performance [rendering](./rendering.md) of standard visual primitives. By leveraging this factory, you benefit from automatic memory management, shader resource pooling, and optimized state updates, making it the preferred approach over manual visual instantiation.

## Internal Architecture and Thread Safety

The `VisualFactory` operates as a thread-safe singleton that bridges the gap between the application's main thread and the dedicated render thread. It handles the serialization of visual property updates, ensuring that heavy resource creation tasks do not block the application's main loop.

The internal architecture utilizes a task-based dispatch system to offload GPU resource preparation—such as texture uploading and buffer allocation—to the render thread. While the `VisualFactory` API itself is thread-safe, external state changes to [visuals](./visuals.md) must be orchestrated through the main thread to maintain synchronization with the scene graph.

## Visual Lifecycle Management

Visuals managed by the factory follow a strict lifecycle governed by reference counting and implicit scene registration. When a visual is created, the `VisualFactory` tracks its usage, ensuring that internal buffers and GPU memory are lazily allocated upon the first display [update](./update.md) and released when no longer required by any active view.

> Note: To ensure predictable performance, developers should avoid manual deletion of visual handles. Relying on smart pointer semantics and the factory's internal cache ensures that resource deallocation occurs during idle frames, preventing hitches in the [rendering](./rendering.md) loop.

## [Shader](./shader.md) Compilation and Optimization

To eliminate runtime jank caused by shader compilation, the `VisualFactory` supports a warm-up mechanism that prepares the graphics pipeline before a visual is rendered to the screen. By utilizing `PrecompileShaderOption`, developers can provide the engine with the necessary metadata to compile shaders ahead of time.

This optimization is particularly critical during heavy UI transitions or when loading complex visual sets. Proper use of shader pre-compilation shifts the CPU and GPU load away from the critical path of the [animation](./animation.md), significantly improving the frame rate consistency of the application.

## Integration API for Engine Developers

For developers extending the engine with custom visual types, the `Dali::Ui::Integration::Visual` interface provides the necessary hooks to register new visual implementations with the `VisualFactory`. This interface allows for deep integration into the [rendering](./rendering.md) pipeline while adhering to the factory’s established lifecycle policies.

Extending the factory requires implementation of the `OnInitialize` and related lifecycle methods to ensure that custom visual types report their resource requirements correctly to the engine’s internal managers.

→ See: [AbsoluteLayoutImpl]

## Visual Base Configuration

The `Dali::Ui::Visual::Base` class acts as the foundational entity for all [visuals](./visuals.md) managed by the factory. The factory configures these [visuals](./visuals.md) through property maps, allowing for a declarative approach to setting visual attributes such as color, size, and layout behavior.

Configuration is performed using property keys that map directly to the underlying visual's internal state. This configuration pattern ensures that visual properties are applied atomically, preventing incomplete states from being submitted to the GPU.

## Factory Performance Tuning

Optimizing the `VisualFactory` involves minimizing the number of unique visual instances and maximizing the reuse of cached resources. Developers should favor the use of property-based visual updates over recreating visual objects, as this allows the factory to reuse existing buffer data and state descriptors.

> Warning: Excessive creation and destruction of visual objects in a single frame will trigger frequent resource re-allocation, leading to memory fragmentation and potential GPU stalls. Always attempt to pool or hide [visuals](./visuals.md) that are not currently in the visible viewport.

### Example: Basic [Layout](./layout.md) Integration

The following example demonstrates how layout configuration interacts with the engine's management systems.

```cpp
#include <dali/ui/absolute-layout.h>
#include <dali/ui/absolute-layout-params.h>

void ConfigureVisualLayout()
{
    // Create an AbsoluteLayout manager for the view
    Dali::Ui::AbsoluteLayout layout = Dali::Ui::AbsoluteLayout::New();

    // Create parameters to define specific child constraints
    Dali::Ui::AbsoluteLayoutParams params = Dali::Ui::AbsoluteLayoutParams::New();
    params.SetX(10.0f);
    params.SetY(10.0f);
    params.SetWidth(100.0f);
    params.SetHeight(100.0f);

    // The layout and params are now ready to be assigned to a View 
    // or passed to the rendering infrastructure.
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/visual-factory)
