---
id: text
title: "text"
sidebar_label: "text"
---
## Introduction to the DALi Text Module

The DALi Text module provides a comprehensive engine for [rendering](./rendering.md), shaping, and styling complex typography. It manages the lifecycle of [text](./text.md)-based visual elements, allowing developers to integrate high-performance [text](./text.md) [rendering](./rendering.md) into their UI applications with support for advanced styling, internationalization, and decorative elements like underlines.

Text in DALi is distinct due to its separation of data, layout, and [rendering](./rendering.md) concerns. By utilizing an internal `Impl` pattern, the module maintains a clear boundary between the developer-facing `public-api` and the performance-critical [rendering](./rendering.md) pipeline, ensuring that typography updates remain efficient even under heavy load.

## Internal Architecture and Rendering Pipeline

The [rendering](./rendering.md) pipeline in the Text module translates abstract character data into pixel-perfect glyphs through a multi-stage process involving shaping, layout calculation, and buffer composition. This architecture ensures that changes to [text](./text.md) properties—such as alignment or wrapping—are processed efficiently without requiring a complete engine-wide restart.

Text [rendering](./rendering.md) relies on the interplay between the layout engine and the texture manager. When [text](./text.md) properties are updated, the module recalculates the required geometry and schedules a texture [update](./update.md) via the `Dali::Ui::TextureManager`.

> Note: For complex [text](./text.md) [layouts](./layouts.md), internal caching mechanisms are employed to avoid redundant shaping operations. Developers should avoid rapidly mutating large strings to maintain 60fps [rendering](./rendering.md) performance.

## Text Styling and Attribute Management

The `Dali::Ui::Text` namespace provides a variety of enumerations and type definitions to control the appearance and behavior of [text](./text.md) strings. These configurations determine how characters flow within their container boundaries and how they are presented visually.

### Alignment and Wrapping Strategies
The module exposes `Alignment`, `LineWrapMode`, and `LineHeightMode` to dictate typography behavior within a bounding box. Additionally, `Direction` and `LayoutDirectionMode` handle right-to-left (RTL) or left-to-right (LTR) scripts, which are essential for globalized applications.

### Font Attributes
The module uses specific type definitions for font configuration:
- `FontWeight`: Controls the boldness or thickness of the typeface.
- `FontWidth`: Adjusts the horizontal stretching of characters.
- `FontSlant`: Determines the italicization or tilt of the glyphs.

## Underline Implementation and Integration

The `Dali::Ui::Text::Underline` class is the primary interface for managing decorative underlining. It encapsulates properties such as color, thickness, and dashed styling within an internal `Impl` class, ensuring that style modifications are decoupled from the core [text](./text.md) [rendering](./rendering.md) [object](./object.md).

### Configuring Underline Properties
The `Underline` class provides a fluent API for styling decorative lines beneath [text](./text.md).

```cpp
#include <dali/ui/text/underline.h>

void SetupUnderline() {
    Dali::Ui::Text::Underline underline;
    // Set color to blue, thickness to 2.0f, and type to solid
    underline.SetColor(Dali::Ui::UiColor(0.0f, 0.0f, 1.0f, 1.0f))
             .SetThickness(2.0f)
             .SetType(Dali::Ui::Text::Underline::Type::Solid);
             
    // For dashed lines, configure additional attributes
    underline.SetDashLength(4.0f)
             .SetDashGap(2.0f);
}
```

> Warning: When using `Type::Dashed`, ensure `SetDashLength` and `SetDashGap` are explicitly set; default values may not be visible depending on the render state.

## Lifecycle and Thread Safety Considerations

Text components in DALi follow a managed object lifecycle. Because text properties are often updated via high-level UI logic, developers must adhere to thread-safety rules: all direct modifications to `Text` properties must occur on the Main Thread (the UI thread).

If text data must be fetched or updated from a background worker thread, you must dispatch a task to the main thread to update the `Text` property. The `Impl` architecture handles the transition from property setting to the rendering stage, ensuring that the background rendering thread is never left in an inconsistent state during layout calculation.

## Integration API Usage and Component Binding

The integration layer allows for high-performance updates by binding custom texture handles directly to text rendering nodes. Using `Dali::Ui::[TextureManager](./texture-manager.md)::AddTexture`, developers can inject custom-rendered texture buffers into the text rendering pipeline.

```cpp
#include <dali/ui/texture_manager.h>

void BindTextureToText(Dali::Texture& customTexture) {
    // Add custom texture to the manager for use in text rendering hooks
    Dali::String textureId = Dali::Ui::TextureManager::AddTexture(customTexture, false);
}
```

→ See: [TextureManager]

## Advanced Text Property Configuration

Advanced configurations allow for the manipulation of text state through the `Impl` structure. By modifying the `Impl` members directly, you can bypass specific high-level validation logic, provided you manually trigger the layout update or notify the renderer of the change.

### Accessing Internal Implementation
While the public API covers standard use cases, the `Dali::Ui::Text::Underline::Impl` struct allows fine-grained access to state variables like `mColor` and `mThickness`.

```cpp
// Example of accessing the Impl layer (Advanced/Internal use only)
void ModifyUnderlineDirectly(Dali::Ui::Text::Underline& underline) {
    // Note: Direct Impl access should only be used when implementing
    // custom text-rendering extensions or performance-critical overrides.
    // Ensure the parent component is notified of changes.
}
```

> Note: Accessing the `Impl` struct directly is intended for framework extensions. In standard application development, always use the public setter methods (`SetColor`, `SetThickness`, etc.) to ensure state consistency.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/text)
