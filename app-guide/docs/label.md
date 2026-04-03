---
id: label
title: "Label"
sidebar_label: "Label"
---
## Understanding the [Label](./label.md) Component

The `Label` component is a specialized, non-editable `View` designed exclusively for [rendering](./rendering.md) [text](./text.md) within the DALi framework. It provides a lightweight solution for displaying static content such as headers, descriptions, or status indicators.

Use `Label` when your primary requirement is the efficient, performant presentation of [text](./text.md). Unlike more complex input-focused components, `Label` is optimized for layout and styling, making it the standard choice for all non-interactive [text](./text.md) display tasks. → See: [TextEditor] (for editable [text](./text.md)).

## Creating and Configuring Labels

Instantiating a `Label` involves creating the [object](./object.md) and applying core styling properties to define its visual appearance. These settings control how the [text](./text.md) is rendered in the UI tree.

### Basic Initialization
To create a `Label`, simply instantiate the class. You can then chain configuration methods to define the initial state, such as [text](./text.md) content, font styling, and color.

```cpp
#include <dali/ui/label.h>

// Create and configure a Label
Dali::Ui::Label myLabel = Dali::Ui::Label();
myLabel.SetText("Hello, World!")
       .SetFontFamily("Arial")
       .SetFontSize(24.0f)
       .SetTextColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 1.0f));
```

### Methods for Styling
*   **SetText(const Dali::String& text)**: Updates the string content displayed by the label.
*   **SetFontFamily(const Dali::String& fontFamily)**: Defines the typeface.
*   **SetFontSize(float fontSize)**: Sets the text height in points.
*   **SetTextColor(const UiColor& color)**: Sets the color of the text using an `UiColor` object.

## Controlling Text Layout and Alignment

Layout properties define how text behaves relative to the boundaries of the `[Label](./label.md)` component. Proper alignment is critical for maintaining visual consistency across varying screen densities.

### Positioning Text
You can influence the placement of text within the bounds of the `[Label](./label.md)` using horizontal and vertical alignment methods.

```cpp
// Set the text to be centered both horizontally and vertically
myLabel.SetHorizontalTextAlignment(Dali::Text::Alignment::CENTER);
myLabel.SetVerticalTextAlignment(Dali::Text::Alignment::CENTER);

// Set custom line height for better readability
myLabel.SetLineHeight(1.5f);
myLabel.SetLineHeightMode(Dali::Text::LineHeightMode::MULTIPLIER);
```

*   **SetHorizontalTextAlignment(Text::Alignment alignment)**: Sets the horizontal alignment (e.g., `BEGIN`, `CENTER`, `END`).
*   **SetVerticalTextAlignment(Text::Alignment alignment)**: Sets the vertical alignment within the allocated height.
*   **SetLineHeight(float lineHeight)**: Defines the vertical space between lines of text.
*   **SetLineHeightMode(Text::LineHeightMode mode)**: Determines if the line height is treated as an absolute value or a multiplier.
*   **SetLayoutDirectionMode(Text::LayoutDirectionMode mode)**: Sets the base direction of the text layout, which is useful for supporting internationalization (e.g., Right-to-Left languages).

## Handling Multiline Text and Wrapping

When content exceeds the width of a label, you must configure how the overflow is managed. Enabling multiline support allows the text to flow onto subsequent lines rather than being truncated.

### Enabling Multiline
Use `SetMultiLine(bool)` to toggle between single-line and multiline modes, and `SetLineWrapMode` to define how the text breaks.

```cpp
// Allow the label to break into multiple lines
myLabel.SetMultiLine(true);
myLabel.SetLineWrapMode(Dali::Text::LineWrapMode::WORD);
```

> **Note**: When `SetMultiLine` is set to `false`, the text is restricted to a single line; any excess text will be clipped according to the label's dimensions.

## Advanced Text Styling with Markup

Markup support allows for the creation of rich text, where specific spans of text can have unique formatting applied within a single `[Label](./label.md)` instance.

### Enabling Markup
To use tags, you must explicitly enable them. Once enabled, you can provide strings containing markup syntax to define styles like color, font weight, or links.

```cpp
myLabel.SetMarkupEnabled(true);
myLabel.SetText("This is <b>bold</b> and this is a <a href='link'>link</a>.");

// Configure link colors
myLabel.SetAnchorColor(Dali::Ui::UiColor(0.0f, 0.0f, 1.0f, 1.0f));
myLabel.SetAnchorClickedColor(Dali::Ui::UiColor(1.0f, 0.0f, 0.0f, 1.0f));
```

*   **SetMarkupEnabled(bool)**: Toggles the parser for markup tags.
*   **SetAnchorColor(UiColor)**: Sets the default display color for hyperlinks defined in the markup.
*   **SetAnchorClickedColor(UiColor)**: Sets the color used when an anchor is being interacted with.

## Summary of Label Best Practices

*   **Performance**: Use `[Label](./label.md)` for static text. If you require user input, switch to the appropriate input-capable view. 
*   **Layout**: Always set `SetMultiLine(true)` if you expect variable-length content that exceeds the component's width.
*   **Markup**: Keep markup strings simple to ensure the parser processes them efficiently; avoid deeply nested tags.
*   **Directionality**: Ensure `SetLayoutDirectionMode` is configured correctly if your application supports regions with different reading directions (e.g., Arabic/Hebrew vs. English).

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/label)
