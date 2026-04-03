---
id: visual-factory
title: "visual-factory"
sidebar_label: "visual-factory"
---
## Introduction to Visual Factory

The `VisualFactory` acts as the central mechanism within the DALi framework for instantiating and managing visual elements. By decoupling the definition of a visual from its underlying [rendering](./rendering.md) implementation, it enables developers to create complex UI components through standardized property configurations.

You should use `VisualFactory` whenever you need to dynamically generate visual content such as [images](./images.md), colors, or animated media within your application views. Its primary distinction is the use of structured data maps to define visual appearances, which allows for highly reusable and maintainable UI code compared to manual [object](./object.md) assembly.

## Creating Visuals with VisualFactory

The `VisualFactory` interface is designed to ingest property maps, transforming them into ready-to-render visual handles. This approach allows you to define complex [rendering](./rendering.md) configurations in a data-driven manner before they are attached to the scene graph.

### Instantiating Visuals
To create a visual, you provide a property map that specifies the type of visual (e.g., Image, Color, Gradient) and its associated parameters. 

> Note: While the `VisualFactory` provides the mechanism for creation, ensuring that the properties passed to the map correspond to the expected visual type is critical to prevent instantiation failures.

```cpp
#include <dali/dali.h>
#include <dali/public-api/visuals/visual-factory.h>

// Example: Creating a simple color visual
Dali::Property::Map propertyMap;
propertyMap.Insert(Dali::Toolkit::Visual::Property::TYPE, Dali::Toolkit::Visual::COLOR);
propertyMap.Insert(Dali::Toolkit::ColorVisual::Property::MIX_COLOR, Dali::Vector4(1.0f, 0.0f, 0.0f, 1.0f));

Dali::Toolkit::Visual::Base visual = Dali::Toolkit::VisualFactory::Get().CreateVisual(propertyMap);
```

## Managing Visual Properties

Once a visual is created, the `Visual::Base` class provides the foundation for managing its lifecycle and appearance. This class acts as a handle that allows you to interact with the visual's internal properties, such as transform, size, and clipping settings.

### Manipulating Visual State
Using `Visual::Base`, you can update the visual's parameters dynamically. This is particularly useful for animations or responding to user input where the visual's properties need to change in real-time.

> Warning: Always check if the handle is valid using `bool` conversion or `!empty()` before attempting to access its methods to avoid runtime errors on uninitialized visuals.

```cpp
// Example: Modifying an existing visual's properties
if (myVisual)
{
  // Apply a new transform to an existing visual
  Dali::Property::Map transform;
  transform.Insert(Dali::Toolkit::Visual::Transform::Property::SIZE, Dali::Vector2(100.0f, 100.0f));
  myVisual.SetTransform(transform);
}
```

## Optimizing Shader Compilation

Performance during application startup is critical for a smooth user experience. The `PrecompileShaderOption` allows developers to hint at the shader requirements of the application, ensuring the necessary shaders are compiled and cached before the UI becomes interactive.

### Defining Shader Requirements
By specifying shader types and configurations early, you prevent "jank" caused by on-demand shader compilation during initial frame rendering. 

> Platform-level detail: This involves interacting with the underlying graphics pipeline; refer to the platform guide for specific details on shader memory constraints and hardware-specific compilation limits.

```cpp
// Example: Hinting at a required shader configuration
Dali::Toolkit::VisualFactory factory = Dali::Toolkit::VisualFactory::Get();
Dali::Toolkit::PrecompileShaderOption options;

options.type = Dali::Toolkit::Visual::IMAGE;
options.isRoundedCornerRequired = true;

factory.PrecompileShader(options);
```

## Visual Lifecycle and Best Practices

Maintaining optimal memory and [rendering](./rendering.md) performance requires a disciplined approach to the lifecycle of visual objects. Because [visuals](./visuals.md) can be resource-intensive, particularly those involving textures or complex shaders, it is essential to manage them efficiently.

### Creation and Disposal
- **Lazy Loading**: Only instantiate [visuals](./visuals.md) that are currently visible on the screen or required for imminent transitions.
- **Resource Cleanup**: When a view or visual is no longer needed, ensure the handle is reset or cleared. This allows the DALi resource manager to reclaim memory associated with textures and geometry.
- **Reuse**: Where possible, [update](./update.md) existing [visuals](./visuals.md) with new property maps instead of destroying and recreating them, as this minimizes allocation overhead.

→ See: [VisualFactory] lifecycle management for disposal patterns and resource caching policies.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/visual-factory)
