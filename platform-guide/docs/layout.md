---
id: layout
title: "Layout"
sidebar_label: "Layout"
---
## Introduction to Input Method Layouts

The `Dali::InputMethod::NormalLayout` serves as the primary mechanism within the DALi InputMethod framework for defining the visual representation and functional set of an on-screen keyboard. You should use `NormalLayout` when you need to specify a particular keyboard variant—such as numeric pads, email-optimized [layouts](./layouts.md), or standard QWERTY—to match the expected user input for a given `TextField` or `TextEditor`. It is distinct from generic view [layouts](./layouts.md) in that it interfaces directly with the platform's input method editor (IME) to ensure the hardware or software keyboard provides the appropriate key mapping and constraints.

## NormalLayout Type Definitions

The `NormalLayout::Type` enumeration dictates the specific configuration of the keyboard interface, allowing developers to optimize the input experience for the user's current task. Selecting the appropriate type ensures that the input method correctly maps touch [events](./events.md) to the expected character set.

### Available Types
The `Type` enum provides variations that influence the platform-level [rendering](./rendering.md) of the input interface.

*   `NormalLayout::Type` (Enum): Represents the variations available for standard input [layouts](./layouts.md).

> Note: The specific visual manifestation of these types is platform-dependent; the engine maps these identifiers to the native IME's corresponding layout resource.

```cpp
#include <dali/dali.h>
#include <dali/devel-api/text-input/input-method-context.h>

// Example: Configuring an InputMethodContext with a specific layout
void SetupInputLayout(Dali::InputMethodContext& context)
{
  // Set the layout type to one of the predefined variations
  // Note: Usage depends on specific internal mapping for the IME
  context.SetLayout(Dali::InputMethod::NormalLayout::Type::DEFAULT);
}
```

## Internal Layout Lifecycle and State Management

The DALi engine manages the lifecycle of `NormalLayout` objects through the `InputMethodContext` during the focus transition process. When a UI element (like a `TextField`) gains focus, the engine initiates a negotiation with the platform's input service, where the `NormalLayout` state is queried to determine the appropriate keyboard constraints.

The engine ensures that if the `NormalLayout` configuration is modified while the input context is active, a re-layout signal is dispatched to the IME to update the visual representation without needing to re-instantiate the entire view hierarchy. This state management is encapsulated within the `InputMethodContext` lifecycle, ensuring that memory usage remains efficient during rapid focus switching between input fields.

## Integration API and Thread Safety

Interaction with `NormalLayout` configurations must be performed on the Main (UI) thread, as these operations frequently trigger cross-process communication with the system's Input Method Service. While the `NormalLayout` definitions are simple enumerations, the `InputMethodContext` methods that utilize these layouts are not inherently thread-safe and must be guarded by the application's main loop.

> Warning: Calling layout configuration methods from a worker thread will lead to undefined behavior or engine-level assertions due to the underlying reliance on the main thread's message queue for native platform calls.

When integrating with custom input providers, ensure that all property changes influencing the layout are dispatched via `Dali::PostCallback` or similar mechanisms if they originate from background data processing tasks.

## Layout Configuration and Property Binding

Binding a `NormalLayout` to an input field is achieved by associating the layout type with the `InputMethodContext` associated with the view. The engine treats these bindings as dynamic properties; changing the `NormalLayout::Type` at runtime will trigger an immediate update in the IME.

### Binding Patterns
To bind a layout, obtain the `InputMethodContext` from the actor and apply the desired type.

```cpp
// Example: Dynamically updating layout for a TextField
void UpdateKeyboardForField(Dali::Toolkit::TextField& field, Dali::InputMethod::NormalLayout::Type newType)
{
  Dali::InputMethodContext context = field.GetInputMethodContext();
  
  if (context)
  {
    // Bind the new layout type to the context
    context.SetLayout(newType);
    
    // The engine automatically triggers a refresh of the IME 
    // to reflect the configuration change.
  }
}
```

→ See: [InputMethodContext](https://developer.tizen.org/dev-guide/latest/org.tizen.native.dali/classDali_1_1InputMethodContext.html) for details on context handling.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/layout)
