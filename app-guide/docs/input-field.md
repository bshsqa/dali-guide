---
id: input-field
title: "InputField"
sidebar_label: "InputField"
---
## Introduction to [InputField](./input-field.md)

The `InputField` is a specialized `View` designed for single-line editable [text](./text.md) entry within a DALi application. Unlike standard static [text](./text.md) labels, `InputField` provides an interactive surface that handles focus, virtual keyboard invocation, and cursor management, making it the primary choice for forms, search bars, and user profile inputs.

It is distinct from other `View` variants by its built-in [text](./text.md) editing capabilities, including placeholder management, [text](./text.md) selection highlights, and automated signal handling for content updates.

## Creating and Configuring

To use an `InputField`, you must instantiate it and integrate it into your current UI hierarchy by adding it to a parent container. As a descendant of `View`, it inherits base layout properties while providing specific initialization paths for [text](./text.md) input.

### Instantiating an [InputField](./input-field.md)

The `InputField` class provides a standard constructor to create an uninitialized handle. Once initialized, you can manipulate its state immediately.

```cpp
#include <dali-ui/dali-ui.h>

void CreateMyInput(Dali::Ui::View parent)
{
  Dali::Ui::InputField myInput; // Create uninitialized handle
  myInput = Dali::Ui::InputField(); // Initialize
  
  parent.Add(myInput);
}
```

## Text Formatting and Style

Proper text styling ensures that user input matches the visual design of your application. The `[InputField](./input-field.md)` allows for granular control over typography, including font attributes and text positioning.

### Typography and Colors

You can define the font appearance and color to ensure readability. 

- **SetFontFamily**: Defines the font type (e.g., "Arial").
- **SetFontSize**: Sets the scale of the text using a `float`.
- **SetTextColor**: Controls the color of the active text using a `UiColor` object.

```cpp
Dali::Ui::InputField input;
input.SetFontFamily("Roboto")
     .SetFontSize(24.0f)
     .SetTextColor(Dali::Ui::UiColor(0.0f, 0.0f, 0.0f, 1.0f)); // Black
```

### Alignment

Alignment settings control how text is positioned within the bounds of the `[InputField](./input-field.md)` component.

- **SetHorizontalTextAlignment**: Sets horizontal positioning (Left, Center, Right) using `Text::Alignment`.
- **SetVerticalTextAlignment**: Sets vertical positioning (Top, Center, Bottom) using `Text::Alignment`.

```cpp
input.SetHorizontalTextAlignment(Dali::Text::Alignment::CENTER);
input.SetVerticalTextAlignment(Dali::Text::Alignment::CENTER);
```

## Managing User Input Constraints

To maintain data integrity, you can apply constraints on how much text a user can enter and provide visual guidance when the field is empty.

### Placeholders and Length Limits

A placeholder is essential for guiding the user, while length limits prevent buffer overflow or data format errors.

- **SetPlaceholder**: Sets the text displayed when no user input exists.
- **SetMaximumLength**: Defines the character limit (integer).

```cpp
Dali::Ui::InputField userInput;
userInput.SetPlaceholder("Enter your name...")
         .SetPlaceholderColor(Dali::Ui::UiColor(0.5f, 0.5f, 0.5f, 1.0f))
         .SetMaximumLength(20);
```

> Note: If the user types beyond the `MaximumLength`, the `[InputField](./input-field.md)` will prevent further character insertion.

## Customizing the Interaction Elements

The interactive elements of the `[InputField](./input-field.md)`, such as the cursor and selection highlighting, can be themed to match your brand identity.

### Cursor and Selection Styling

- **SetCursorWidth**: Sets the pixel width of the vertical cursor bar.
- **SetCursorColor**: Defines the `UiColor` of the blinking cursor.
- **SetSelectionColor**: Defines the `UiColor` used to highlight text when a user performs a long-press or drag selection.

```cpp
input.SetCursorWidth(2)
     .SetCursorColor(Dali::Ui::UiColor::Blue)
     .SetSelectionColor(Dali::Ui::UiColor(0.0f, 0.0f, 1.0f, 0.3f));
```

## Common Use Cases and Best Practices

Handling user input requires responding to state changes. The most common way to react to input is by monitoring the `TextChangedSignal`.

### Reacting to Text Changes

The `TextChangedSignal` is emitted whenever the user types, deletes, or pastes text into the field. Use this to perform real-time validation or state updates.

```cpp
void OnTextChanged(Dali::Ui::View view)
{
    Dali::Ui::InputField input = static_cast<Dali::Ui::InputField>(view);
    Dali::String currentText = input.GetText();
    // Logic to validate text length or content
}

// Inside your initialization method:
input.TextChangedSignal().Connect(&OnTextChanged);
```

### Best Practices
* **Performance**: Avoid complex operations inside the `TextChangedSignal` callback to ensure the keyboard remains responsive.
* **Layout Direction**: For internationalization support, use `SetLayoutDirectionMode` if your application targets both Left-to-Right and Right-to-Left languages.
* **Sibling Components**: When grouping multiple inputs, ensure you manage focus transitions appropriately. → See: [View]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/input-field)
