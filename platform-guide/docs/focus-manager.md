---
id: focus-manager
title: "focus-manager"
sidebar_label: "focus-manager"
---
## Introduction to the Focus Manager

The `KeyboardFocusManager` is the central authority within the DALi framework for managing spatial navigation and input focus state. It provides a robust mechanism to track which actor currently holds the "keyboard focus" and coordinates how focus transitions between actors in response to directional navigation [events](./events.md).

Developers use the `focus-manager` to create accessible and navigable user interfaces, particularly for non-pointer input devices like D-pads, remote controls, or keyboards. It is distinct because it operates independently of the visual hierarchy, allowing for a decoupled "focus chain" that can transcend the parent-child relationship of the scene graph.

## Internal Architecture and Lifecycle

The `KeyboardFocusManager` is implemented as a singleton [object](./object.md), ensuring a unified state of focus across the entire application instance. Its lifecycle is managed by the DALi core, initializing during the setup of the application environment and persisting throughout the application's runtime.

The manager integrates directly into the DALi event pipeline, intercepting key-event [signals](./signals.md) before they are propagated to individual actors. By hooking into the engine's [update](./update.md) loop, it synchronizes the focus state with visual changes, ensuring that if an actor becomes off-stage or disabled, the focus is either cleared or transferred gracefully according to established navigation policies.

## Core Public and Devel APIs

This section covers the primary methods used for standard focus manipulation and state observation. Access the singleton instance via `KeyboardFocusManager::Get()`.

### Managing Focus State
The following methods control the active focus of the application.

*   **SetCurrentFocusActor**: Moves the keyboard focus to the specified actor.
    *   **Parameters**: `Actor actor` - The actor to receive focus.
    *   **Returns**: `bool` - `true` if the focus change was successful, `false` otherwise.
*   **GetCurrentFocusActor**: Retrieves the actor that currently has keyboard focus.
    *   **Returns**: `Actor` - The currently focused actor, or an empty handle if no focus exists.

```cpp
#include <dali/dali.h>
#include <dali/devel-api/adaptor-framework/keyboard-focus-manager.h>

void SetFocusToButton(Dali::Actor myButton) {
    auto manager = Dali::Ui::KeyboardFocusManager::Get();
    if (manager.SetCurrentFocusActor(myButton)) {
        // Success: Button now has focus
    }
}
```

### Signal Handling
Signals allow applications to respond to changes in the focus state.

*   **FocusChangedSignal**: Emitted after the focus actor changes.
    *   **Usage**: Connect a callback to observe when the user navigates between components.

```cpp
void OnFocusChanged(Dali::Actor original, Dali::Actor current) {
    // Respond to focus transition
}

// Connecting to the signal
Dali::Ui::KeyboardFocusManager::Get().FocusChangedSignal().Connect(&OnFocusChanged);
```

### Focus Group Configuration
Focus groups allow you to constrain navigation to a specific sub-tree of the scene graph.

*   **SetAsFocusGroup**: Designates an actor as a focus group container.
*   **SetFocusGroupLoop**: Toggles whether navigation wraps around the edges of a focus group.

> Note: When an actor is a focus group, the `KeyboardFocusManager` treats its children as a distinct navigational context, preventing focus from "leaking" to other parts of the scene graph unless the group is exited.

## Spatial Navigation and FocusFinder

The `FocusFinder` is an internal engine component that automatically calculates the next focusable actor when a directional navigation signal (Up, Down, Left, Right) is triggered. It performs geometric analysis to find the actor closest to the current focus in the target direction.

### Manual Traversal
*   **MoveFocus**: Triggers the `FocusFinder` to select the next actor in a specific direction.
    *   **Parameters**: `FocusDirection direction` - The intended navigation path.
    *   **Returns**: `bool` - `true` if a new target was found and focus moved, `false` otherwise.

## Implementing Custom Focus Algorithms

For complex layouts where the geometric `FocusFinder` logic is insufficient, you can implement the `CustomAlgorithmInterface`.

### Using CustomAlgorithmInterface
*   **GetNextFocusableActor**: You must implement this to return a custom `Actor` target.
    *   **Parameters**: `Actor current`, `Actor proposed`, `FocusDirection direction`, `const std::string &deviceName`.
    *   **Returns**: `Actor` - The target actor to receive focus.

```cpp
class MyCustomNavigation : public Dali::Ui::DevelKeyboardFocusManager::CustomAlgorithmInterface {
public:
    Dali::Actor GetNextFocusableActor(Dali::Actor current, Dali::Actor proposed, 
                                      Dali::Ui::FocusDirection direction, 
                                      const std::string &deviceName) override {
        // Logic to return a specific actor based on custom constraints
        return someSpecificActor;
    }
};

// Activation
MyCustomNavigation myNav;
Dali::Ui::DevelKeyboardFocusManager::SetCustomAlgorithm(Dali::Ui::KeyboardFocusManager::Get(), myNav);
```

> Warning: Custom algorithms are executed within the update thread. Ensure that your `GetNextFocusableActor` implementation is highly performant to avoid dropping frames.

## Thread Safety and Integration Considerations

The `KeyboardFocusManager` and its associated signals operate primarily on the Main (Event) Thread. All interactions with the manager, including setting the focus, moving focus, and signal connection, must occur on this thread.

If you are using the integration-api to bridge external events, you must use `Dali::EventThreadCallback` or equivalent mechanisms to post focus change requests back to the main thread. Accessing `KeyboardFocusManager` from a background thread is not thread-safe and will lead to undefined behavior in the engine state.

## DALI_INTERNAL Component Mapping

The `DALI_INTERNAL` namespace contains private bridge methods used by the DALi core to interface between high-level GUI components and the focus state machine.

*   **Internal Scope**: These methods are used to track `Visual` focus states within composite widgets.
*   **Usage**: Developers should generally avoid the `DALI_INTERNAL` namespace unless performing advanced integration tasks that require manipulating internal focus-trigger mappings. 

> Note: Relying on `DALI_INTERNAL` features can break binary compatibility across different versions of the DALi framework and is strictly discouraged for application-level code.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/focus-manager)
