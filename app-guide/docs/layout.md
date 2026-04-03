---
id: layout
title: "Layout"
sidebar_label: "Layout"
---
## Introducing Input Method Layouts

The `Dali::InputMethod::NormalLayout` serves as the primary mechanism for defining the keyboard interface behavior within [text](./text.md) input components. You should use `NormalLayout` when your application requires specific input contexts, such as numeric-only entry, email address formatting, or standard alphanumeric input, ensuring the platform keyboard adapts to the user's current task. It is distinct from other input strategies by providing a structured, enumerated approach to keyboard variations that remain consistent across the DALi ecosystem.

## Configuring [Layout](./layout.md) Types

Selecting the correct `NormalLayout::Type` ensures that the user is presented with an optimized keyboard layout tailored to the specific field they are interacting with. By explicitly setting these types, you reduce user error and improve data entry efficiency.

### Available [Layout](./layout.md) Variations

The `NormalLayout::Type` enum defines the available variations. Developers should map these to their specific [text](./text.md) fields during the initialization phase to ensure the keyboard appears correctly on the first tap.

> Note: The choice of `Type` informs the platform input method; invalid configurations may lead to a fallback to the default keyboard.

```cpp
#include <dali/dali.h>

void ConfigureInput(Dali::InputMethod::NormalLayout::Type layoutType)
{
  // Example of selecting a layout type based on application logic
  Dali::InputMethod::NormalLayout::Type selectedType = layoutType;
  
  // Usage within a text input component initialization
  // (Assuming integration with a TextField or similar input-aware view)
  // myTextField.SetInputMethodLayout(selectedType);
}
```

## Managing Layout Lifecycle

Managing the lifecycle of `NormalLayout` is straightforward, as these types are generally managed as state properties of your text entry components. Best practices dictate defining the layout type alongside your component properties to maintain clear application state.

### Instantiating and Association

While `NormalLayout` is defined within the `Dali::InputMethod` namespace, it is typically applied as a property of a text view. Ensure that you associate the desired layout before the text component receives focus to provide the user with the correct input tools immediately.

```cpp
#include <dali/dali.h>

void InitializeView()
{
  // Apply a specific layout variation to a component
  Dali::InputMethod::NormalLayout::Type numericLayout = Dali::InputMethod::NormalLayout::NUMERIC;
  
  // Associating the layout with a text input component
  // MyTextInputComponent.SetInputLayout(numericLayout);
}
```

## Dynamic Layout Switching

Dynamic switching allows your application to respond to user interactions, such as toggling between standard text and password entry, or shifting to a numeric pad when a specific field receives focus. This fluidity is key to maintaining a professional user experience.

### Programmatic Switching

To switch layouts, simply update the layout type associated with your input component when the input state changes. This is typically done within an event listener that detects focus changes or specific user requests.

```cpp
void OnFocusReceived(Dali::InputMethod::NormalLayout::Type newType)
{
  // Update the component to the new requested layout
  // myTextField.SetInputMethodLayout(newType);
}
```

## Handling Layout Events and Signals

Monitoring input events is crucial for maintaining synchronization between the UI and the input method. While the layout itself represents the configuration, your components will emit signals when input changes occur, allowing you to react to the current layout state.

### Monitoring Input Changes

You should monitor signals from your input components to ensure the UI updates reflect the active `NormalLayout`. By listening for state changes, you can trigger internal logic to adjust visibility or validation rules based on the currently active keyboard layout.

> Warning: Avoid performing heavy operations within layout-change signal handlers, as these are triggered during high-frequency user interactions.

```cpp
// Example of connecting to a signal that indicates layout change or input state change
// myTextField.ConnectInputMethodChanged([](Dali::InputMethod::NormalLayout::Type currentType) {
//   if(currentType == Dali::InputMethod::NormalLayout::NUMERIC) {
//     // Update validation logic for numeric input
//   }
// });
```

→ See: [TextField] (Standard view for [text](./text.md) entry)

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/layout)
