---
id: text
title: "text"
sidebar_label: "text"
---
## Introduction to Text Components

The DALi Text module provides the fundamental building blocks for [rendering](./rendering.md), styling, and managing textual content within a graphical user interface. It is designed to handle complex typographic requirements, including multi-line wrapping, dynamic styling, and decorative effects like underlines, ensuring high-performance [text](./text.md) [rendering](./rendering.md) across diverse screen resolutions.

You should use these components whenever your application requires static labels, editable [text](./text.md) input, or dynamic information display. The DALi [text](./text.md) system is distinct because it integrates directly into the visual scene graph, allowing [text](./text.md) to participate in coordinate transformations, animations, and clipping logic just like any other visual element.

## Creating and Displaying Text

Displaying [text](./text.md) in DALi is achieved by leveraging the `TextVisual` namespace, which defines the properties necessary to render [text](./text.md) content within the scene graph. By configuring these visual properties, you define how strings are transformed into pixels on the screen.

### Rendering Text with TextVisual

The `TextVisual` namespace provides the necessary property keys to define [text](./text.md) content, which is then assigned to visual components (→ See: [Visuals]). To display [text](./text.md), you map string data and style settings to the `TextVisual::Property` index, allowing the engine to handle the layout and glyph rasterization.

> Note: `TextVisual` is a specialized visual type; ensure it is attached to an actor that has been added to the root of your application's scene graph.

```cpp
// Example: Configuring a text visual property
// Note: This assumes an existing control or actor where the visual is applied.
Property::Map textMap;
textMap.Insert(TextVisual::Property::TEXT, "Hello DALi Text");
textMap.Insert(TextVisual::Property::POINT_SIZE, 12.0f);

// Apply the map to a visual component
myControl.SetProperty(Control::Property::BACKGROUND, textMap);
```

## Configuring Text Styles

Text style APIs allow you to manage font attributes, text color, and layout properties. Proper configuration ensures that your application maintains consistent typography across different design contexts.

### Fundamental Style Attributes

While primary text properties are managed via `TextVisual::Property`, internal layout is governed by the enumerations provided in the `Dali::Ui::Text` namespace. These enumerations allow you to dictate how text behaves when it exceeds the width or height of its container.

- **Alignment**: Use `Dali::Ui::Text::Alignment` to define horizontal or vertical text positioning.
- **LineWrapMode**: Use `Dali::Ui::Text::LineWrapMode` to determine if text should wrap at character or word boundaries.

## Applying Underline Effects

The `Dali::Ui::Text::Underline` class provides a granular way to apply decorative lines beneath your text. It allows for full customization of the appearance, including thickness and dash patterns.

### Customizing Underline Properties

You can instantiate an `Underline` object to define visual parameters and apply them to text elements. The API supports solid lines as well as custom-defined dashed patterns.

```cpp
// Create and configure an underline effect
Dali::Ui::Text::Underline underline;
underline.SetColor(Color::BLUE);
underline.SetThickness(2.0f);
underline.SetType(Dali::Ui::Text::Underline::Type::DASHED);
underline.SetDashLength(5.0f);
underline.SetDashGap(2.0f);
```

> Warning: When using `Dali::Ui::Text::Underline::Type::DASHED`, both `DashLength` and `DashGap` must be set to ensure the underline renders as intended.

## Leveraging Text Enumerations

The `Dali::Ui::Text` namespace provides a comprehensive set of enumerations to control complex rendering behaviors. These are essential for handling localization and dynamic text sizing.

### Essential Enumeration Reference

- **Direction**: Controls the base layout direction (e.g., Left-to-Right or Right-to-Left).
- **LayoutDirectionMode**: Determines how the layout direction is resolved, particularly useful in multi-lingual applications.
- **LineHeightMode**: Specifies how line spacing is calculated relative to the font size.
- **MarqueeOrientation**: Used for scrolling text effects; defines whether the marquee scrolls along the horizontal or vertical axis.
- **MarqueeStopMode**: Defines the termination behavior of a marquee animation when the scroll sequence concludes.

## Common Text Patterns and Best Practices

When managing dynamic text, performance is critical. Re-rendering large blocks of text frequently can lead to frame drops; therefore, minimize updates to text properties unless the content specifically changes.

### Best Practices for Performance

- **Minimize Property Updates**: Only update text content when the underlying data changes, rather than on every frame update.
- **Use Typedefs for Fonts**: Utilize the provided `FontWeight`, `FontWidth`, and `FontSlant` types to maintain semantic clarity when defining custom font styles.
- **Consistency**: Centralize your `Underline` configurations in a factory or helper class to ensure consistent styling throughout the application UI.

> Note: Advanced [text](./text.md) shaping or platform-specific font [rendering](./rendering.md) features may require platform-level detail; please refer to the platform guide for deep-dive integration scenarios.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/text)
