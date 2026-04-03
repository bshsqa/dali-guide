---
id: label
title: "Label"
sidebar_label: "Label"
---
## Introduction to [Label](./label.md)

The `Dali::Ui::Label` component is a specialized, non-editable View designed for the efficient [rendering](./rendering.md) of static, multi-format [text](./text.md) within the DALi scene graph. Unlike generic view containers, `Label` integrates directly with the DALi [text](./text.md)-shaping engine to provide high-performance typography, including font shaping, bi-directional [text](./text.md) support, and rich [text](./text.md) [rendering](./rendering.md).

Use `Label` whenever your application requires static [text](./text.md) display. It is optimized for scenarios where content is read-only and requires sophisticated visual treatment, such as headers, descriptions, or status indicators. For editable user input, → See: `TextField` or `TextEditor`.

## Text Properties and Configuration

This section covers the core attributes required to define the visual representation of textual content, including the string data itself and basic typographic styling. Use these properties to establish the primary look and feel of the [text](./text.md) within your UI.

### Basic Content and Styling
The [text](./text.md) content and its associated font properties (family and size) are the fundamental building blocks of a `Label`.

* **SetText(const Dali::String& [text](./text.md))**: Updates the string content of the label.
* **SetFontFamily(const Dali::String& fontFamily)**: Defines the font typeface to be used.
* **SetFontSize(float fontSize)**: Specifies the point size of the font.

```cpp
#include <dali/ui/label.h>

void SetupLabel(Dali::Ui::Label& label) {
    label.SetText("Hello, DALi World!")
         .SetFontFamily("SamsungSans")
         .SetFontSize(24.0f)
         .SetTextColor(Dali::Ui::UiColor::BLACK);
}
```

> Note: Changing the font family requires the font to be registered within the system font directory; otherwise, the engine falls back to the system default font.

## Advanced Typography and Alignment

Fine-tuning the text layout within a label's bounds is essential for achieving professional-grade UI polish. This section explains how to manipulate the positioning and vertical spacing of your text.

### Alignment and Line Control
Horizontal and vertical alignment allow you to place text relative to the bounding box of the `[Label](./label.md)`.

* **SetHorizontalTextAlignment(Text::Alignment alignment)**: Sets horizontal positioning (e.g., Begin, Center, End).
* **SetVerticalTextAlignment(Text::Alignment alignment)**: Sets vertical positioning within the label's height.
* **SetLineHeight(float lineHeight)**: Sets the absolute or relative height of each line of text.
* **SetLineHeightMode(Text::LineHeightMode mode)**: Determines if the `lineHeight` is interpreted as a fixed value or a multiplier.

```cpp
void SetAlignment(Dali::Ui::Label& label) {
    label.SetHorizontalTextAlignment(Dali::Text::Alignment::CENTER)
         .SetVerticalTextAlignment(Dali::Text::Alignment::CENTER)
         .SetLineHeight(1.2f)
         .SetLineHeightMode(Dali::Text::LineHeightMode::MULTIPLIER);
}
```

## Multiline and Wrapping Logic

When text exceeds the horizontal boundary of a `[Label](./label.md)`, you must define how the engine handles the overflow. These settings ensure your UI remains responsive and readable regardless of the text length.

### Handling Overflow
* **SetMultiLine(bool multiLine)**: Enables or disables the creation of new lines when space is exhausted.
* **SetLineWrapMode(Text::LineWrapMode mode)**: Dictates the break strategy (e.g., word wrap, character wrap) for text exceeding the width.

```cpp
void EnableMultiline(Dali::Ui::Label& label) {
    label.SetMultiLine(true)
         .SetLineWrapMode(Dali::Text::LineWrapMode::WORD);
}
```

> Warning: Enabling `MultiLine` without specifying a constrained size (width) for the label may lead to unpredictable layout behavior. Ensure the parent container or the label itself has a defined width.

## Markup and Rich Text Formatting

Markup allows for granular control over subsets of the text string, enabling mixed styling like bold, italics, or colored spans within a single `[Label](./label.md)`.

### Enabling Rich Text
When markup is enabled, the `[Label](./label.md)` parses internal tags (e.g., `<b>`, `<color>`) to apply dynamic styles.

* **SetMarkupEnabled(bool enabled)**: Toggles the parser.
* **SetAnchorColor / SetAnchorClickedColor**: Configures the visual state of hyperlinked regions within the markup.

```cpp
void EnableRichText(Dali::Ui::Label& label) {
    label.SetMarkupEnabled(true);
    label.SetText("This is <b>bold</b> and <color value='red'>red</color> text.");
    label.SetAnchorColor(Dali::Ui::UiColor::BLUE);
}
```

## Lifecycle and Engine Integration

The `[Label](./label.md)` is a high-level UI component that manages an internal render-tree branch. Modifying any property triggers a re-layout cycle, where the DALi text-shaping engine recalculates glyph positions, line breaks, and shaping offsets.

### Performance Considerations
Updates to text properties are processed during the next frame's "Update" phase. Because text shaping is a CPU-intensive operation, avoid rapid, frame-by-frame updates to properties like `SetText` or `SetFontSize` to prevent potential frame drops. 

When updating many properties at once, consider the builder-pattern nature of the API—most methods return a reference to the `[Label](./label.md)` (`[Label](./label.md)&`), allowing you to chain updates to minimize the number of internal state-validation passes.

* **Lifecycle**: A `[Label](./label.md)` instance holds an internal handle to a native DALi object. The `[Label](./label.md)` destructor releases these resources.
* **Threading**: All property setters and getters must be called on the Main (UI) thread. Modifying `[Label](./label.md)` properties from background threads will lead to race conditions and undefined behavior in the engine.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/label)
