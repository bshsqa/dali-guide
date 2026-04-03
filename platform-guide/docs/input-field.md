---
id: input-field
title: "InputField"
sidebar_label: "InputField"
---
## Introduction to [InputField](./input-field.md)

The `InputField` is a specialized `View` component designed specifically for single-line [text](./text.md) entry. It encapsulates the complex logic required for [text](./text.md) [rendering](./rendering.md), cursor management, and interaction with system input methods.

You should use `InputField` whenever your application requires user [text](./text.md) input, such as username fields, search bars, or single-line form entries. It is distinct from standard [text](./text.md) labels or multi-line [text](./text.md) views because it inherently handles focus, native input software integration, and [text](./text.md) selection states out of the box. → See: [View]

## Component Lifecycle and Threading Model

The `InputField` lifecycle follows the standard DALi [object](./object.md) model, where handle-based memory management ensures that the underlying resources are released once all handles are destroyed. All mutations to `InputField` properties must be performed on the main event loop thread to ensure consistency within the DALi scene graph.

### Initialization and Memory Management
`InputField` objects are initialized using the default constructor. Because DALi uses a handle-based architecture, you can safely copy or move these handles without duplicating the underlying UI [object](./object.md).

```cpp
#include <dali-toolkit/dali-toolkit.h>

using namespace Dali::Ui;

void CreateInput() {
  // Creating an uninitialized handle
  InputField myInput;
  
  // Initializing via construction
  myInput = InputField();
  
  // Copying handles is lightweight
  InputField anotherHandle = myInput;
}
```

> Warning: Always ensure that `[InputField](./input-field.md)` manipulations, such as setting text or font properties, occur on the thread responsible for the DALi event loop to prevent race conditions during the layout calculation phase.

## Managing Text Content and Constraints

These APIs allow developers to programmatically control the text buffer and enforce entry limitations. By defining character limits, you can ensure that input strictly adheres to your application's data models.

### Content Control
The `SetText` and `GetText` methods provide the primary interface for syncing the `[InputField](./input-field.md)` with your application logic. Use `SetMaximumLength` to restrict the input buffer size, which is essential for performance and database constraints.

```cpp
void ConfigureInput(InputField& input) {
  input.SetText("Default Content");
  input.SetMaximumLength(20);
  
  Dali::String currentText = input.GetText();
}
```

### Detecting Changes
The `TextChangedSignal` allows your application to react dynamically to user input, such as triggering a search query or validating credentials in real-time.

```cpp
void OnTextChanged(View view) {
  // Cast the sender back to InputField if necessary
  InputField input = static_cast<InputField>(view);
  // Perform validation or processing...
}

// Connecting the signal
input.TextChangedSignal().Connect(&OnTextChanged);
```

## Styling and Visual Customization

The styling APIs provide granular control over the typography and aesthetic properties of the text. These methods allow the `[InputField](./input-field.md)` to blend seamlessly into your application's design language.

### Typography and Alignment
You can configure font parameters such as family, size, weight, and slant. Additionally, the horizontal and vertical alignment controls allow you to position the text within the bounds of the `[InputField](./input-field.md)` component.

```cpp
void ApplyStyles(InputField& input) {
  input.SetFontFamily("Roboto")
       .SetFontSize(16.0f)
       .SetFontWeight(Text::FontWeight::BOLD)
       .SetHorizontalTextAlignment(Text::Alignment::CENTER)
       .SetVerticalTextAlignment(Text::Alignment::CENTER)
       .SetTextColor(UiColor(0.1f, 0.1f, 0.1f, 1.0f));
}
```

## Input Interaction and UI Feedback

These APIs define how the user interacts with the text, including the visual cues provided by the cursor, selection highlights, and placeholder text.

### Placeholders and Cursors
Placeholder text provides a hint to the user when the field is empty, improving usability. Customizing the cursor and selection colors ensures that the visual feedback matches your application's branding.

```cpp
void ConfigureInteraction(InputField& input) {
  input.SetPlaceholder("Enter your name...");
  input.SetPlaceholderColor(UiColor(0.5f, 0.5f, 0.5f, 1.0f));
  
  input.SetCursorWidth(2);
  input.SetCursorColor(UiColor::BLUE);
  input.SetSelectionColor(UiColor(0.2f, 0.6f, 1.0f, 0.3f));
}
```

> Note: `SetSelectionColor` affects the background highlight of text chosen by the user, while `SetCursorColor` modifies the vertical bar indicating the current insertion point.

## Platform Integration and Input Flow

The `[InputField](./input-field.md)` automatically bridges the gap between the DALi rendering engine and the platform's native input systems. By handling focus management internally, it ensures that when an `[InputField](./input-field.md)` is activated, the system keyboard is invoked and text events are routed correctly to the component.

### Layout Direction
To support internationalization, the `SetLayoutDirectionMode` allows the field to adjust its flow based on the text direction (e.g., Left-to-Right for English, Right-to-Left for Arabic).

```cpp
void SetupLocalization(InputField& input) {
  // Ensure the input field respects the system's reading order
  input.SetLayoutDirectionMode(Text::LayoutDirectionMode::CONTENT);
}
```

By leveraging these integration points, the `[InputField](./input-field.md)` remains the most efficient way to capture single-line input while maintaining high performance within the DALi scene graph.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/input-field)
