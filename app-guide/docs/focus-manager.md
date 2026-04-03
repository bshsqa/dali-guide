---
id: focus-manager
title: "focus-manager"
sidebar_label: "focus-manager"
---
## Introduction to Focus Manager

The `KeyboardFocusManager` is the central component in the DALi framework responsible for orchestrating input navigation and tracking the currently focused actor. It provides a standardized mechanism to manage a two-dimensional focus chain, allowing users to move through UI elements predictably using hardware keys or remote controls.

You should use the `KeyboardFocusManager` whenever your application requires navigable UI components, such as menus, forms, or grids. It is distinct because it abstracts the complexity of spatial navigation, allowing developers to define logical "focus groups" and automatically handle state transitions when focus shifts between actors.

## Basic Keyboard Navigation

Basic navigation allows users to move the focus cursor between actors using directional input. The `KeyboardFocusManager` manages the traversal order, ensuring that input is routed to the correct UI component.

### Moving Focus
The `MoveFocus` method shifts the focus to the next available actor in the defined traversal path.

**API: `bool MoveFocus(FocusDirection direction)`**
*   **What:** Shifts focus in a specified spatial direction.
*   **Why:** Use this to manually trigger a navigation event, typically when mapping custom controller inputs to the focus system.
*   **Parameters:** `direction` (a `FocusDirection` enum value representing up, down, left, or right).
*   **Return:** Returns `true` if the focus was successfully moved to a new actor, otherwise `false`.

```cpp
auto& focusManager = Dali::Ui::KeyboardFocusManager::Get();
// Attempt to move focus to the right
bool success = focusManager.MoveFocus(Dali::Toolkit::Control::KeyboardFocus::Right);
```

### Moving Backward
For UI flows where a "back" or "undo" navigation is required, you can use the built-in backward move.

**API: `void MoveFocusBackward()`**
*   **What:** Moves the focus to the previously selected actor.
*   **Why:** Essential for implementing "Back" buttons or secondary navigation patterns that return the user to their previous context.

```cpp
Dali::Ui::KeyboardFocusManager::Get().MoveFocusBackward();
```

## Managing Focusable Actors

Before an actor can receive focus, it must be part of the focusable hierarchy. You can manage which actors are eligible for focus and define group boundaries to contain navigation.

### Setting the Current Focus
You can explicitly move the focus to any actor in your application scene.

**API: `bool SetCurrentFocusActor(Actor actor)`**
*   **What:** Sets the specified actor as the currently focused UI element.
*   **Why:** Use this during screen initialization to set a default focus or when context changes require jumping to a specific input field.
*   **Parameters:** `actor` (the `Actor` instance to focus).
*   **Return:** Returns `true` if the focus operation succeeded.

```cpp
Dali::Actor myButton = Dali::Toolkit::PushButton::New();
// Add to scene...
bool focused = Dali::Ui::KeyboardFocusManager::Get().SetCurrentFocusActor(myButton);
```

### Focus Groups
A focus group restricts navigation to a specific subtree of actors, preventing the focus from jumping to unrelated parts of the UI unexpectedly.

**API: `void SetAsFocusGroup(Actor actor, bool isFocusGroup)`**
*   **What:** Marks an actor as a focus group container.
*   **Why:** Use this for complex layouts like sidebars or tab controls where you want to keep focus within a specific panel until the user explicitly navigates out.
*   **Parameters:** `actor` (the container), `isFocusGroup` (true to enable grouping, false to disable).

```cpp
Dali::Actor sidebar = Dali::Toolkit::Control::New();
Dali::Ui::KeyboardFocusManager::Get().SetAsFocusGroup(sidebar, true);
```

## Handling Focus Signals

Signals allow your application to react dynamically to focus changes, such as highlighting an actor or updating text labels when an element is selected.

### Tracking Focus Changes
The `FocusChangedSignal` notifies your application whenever the focus moves from one actor to another.

**API: `FocusChangedSignalType & FocusChangedSignal()`**
*   **What:** Provides a callback whenever the focused actor changes.
*   **Why:** Useful for updating UI effects, such as scaling an actor when it gains focus.

```cpp
void OnFocusChanged(Dali::Actor original, Dali::Actor current) {
  // Logic to handle state changes
}

Dali::Ui::KeyboardFocusManager::Get().FocusChangedSignal().Connect(&OnFocusChanged);
```

### Handling Enter Key
When an actor is focused, the `FocusedActorEnterKeySignal` notifies you if the user presses the 'Enter' or 'Select' key.

**API: `FocusedActorEnterKeySignalType & FocusedActorEnterKeySignal()`**
*   **What:** Emits a signal when the enter key is pressed on a focused actor.
*   **Why:** This is the primary way to trigger actions (like opening a new page) on focused buttons or list items.

## Advanced Focus Finding

The `KeyboardFocusManager` provides utilities to inspect the state of the focus chain, which is useful for debugging navigation or implementing custom UI indicators.

### Focus Indicators
You can set a global "Focus Indicator" actor that follows the currently focused element.

**API: `void SetFocusIndicatorActor(View indicator)`**
*   **What:** Defines the visual actor that serves as the focus highlight.
*   **Why:** Centralizes the look-and-feel of your focus state across the entire application.

```cpp
Dali::Toolkit::ImageView indicator = Dali::Toolkit::ImageView::New("focus_border.png");
Dali::Ui::KeyboardFocusManager::Get().SetFocusIndicatorActor(indicator);
```

## Implementing Custom Focus Algorithms

While the framework provides automatic traversal, you can influence the behavior by managing group loops or querying the focus state.

> Note: For highly complex spatial navigation logic (like non-grid layouts), refer to the platform guide for details on implementing custom focus algorithms via platform-level interfaces.

**API: `void SetFocusGroupLoop(bool enabled)`**
*   **What:** Enables or disables focus looping within a group.
*   **Why:** Set to `true` if you want the focus to wrap around to the first item when the user reaches the end of the list.

```cpp
Dali::Ui::KeyboardFocusManager::Get().SetFocusGroupLoop(true);
```

## Best Practices for Accessible Navigation

1.  **Consistency:** Always set a logical default focus actor when a new view appears using `SetCurrentFocusActor`.
2.  **Visual Feedback:** Ensure your focus indicator is visually distinct and follows the `FocusedActorEnterKeySignal` triggers correctly.
3.  **Boundary Management:** Use `SetAsFocusGroup` to ensure that users do not get lost in deep, complex UI structures.
4.  **Device Awareness:** Use `GetLastFocusChangeDeviceName` to determine if input came from a remote, keyboard, or other input device, allowing you to tailor your UI interactions if necessary.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/focus-manager)
