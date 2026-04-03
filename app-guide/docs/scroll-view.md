---
id: scroll-view
title: "ScrollView"
sidebar_label: "ScrollView"
---
## Understanding [ScrollView](./scroll-view.md)

[ScrollView](./scroll-view.md) is a specialized container designed to display content that exceeds the physical boundaries of its viewport. Use [ScrollView](./scroll-view.md) when you need to provide a scrollable surface for complex [layouts](./layouts.md), long lists, or image galleries that require navigation beyond the screen's dimensions.

Unlike a standard View, [ScrollView](./scroll-view.md) manages internal coordinate transformations and input gesture recognition automatically, enabling smooth panning and fling interactions. It is distinct from other layout components because it acts as a scrollable bridge between your content and the UI hierarchy. 

→ See: [View](View-Reference-Link)

## Configuring Content and [Layout](./layout.md)

You must define the scrollable content to enable the [ScrollView](./scroll-view.md)'s functionality. By setting the content view and the scroll direction, you determine how the component reacts to user input.

### Setting the Content
The `SetContent` method assigns a child `View` that will be moved within the `ScrollView`'s viewport.

```cpp
// Create the container and the content
Dali::Ui::ScrollView scrollView;
Dali::Ui::View content = Dali::Ui::View::New(); 

// Configure content and add to the scroll view
scrollView.SetContent(content);

// Retrieve existing content
Dali::Ui::View currentContent = scrollView.GetContent();
```

### Defining Scroll Direction
The `SetScrollDirection` method allows you to restrict navigation to horizontal, vertical, or bidirectional modes.

* **direction**: An instance of `ScrollDirection` (e.g., `ScrollDirection::Vertical`, `ScrollDirection::Horizontal`).

```cpp
// Configure for vertical-only scrolling
scrollView.SetScrollDirection(Dali::Ui::ScrollDirection::Vertical);
```

## Controlling Scroll Dynamics

Fling dynamics determine the "physical" feel of the scrolling experience, such as how long content glides after a user lifts their finger.

### Configuring Fling Behavior
Use `SetMaxFlingDistance`, `SetFlingSensitivity`, and `SetDecelerationRate` to fine-tune the kinetic scroll animation.

* **distance**: The maximum distance the scroll can travel after a fling gesture.
* **sensitivity**: Higher values make the scroll react faster to input; lower values provide more resistance.
* **rate**: Controls how quickly the scroll velocity approaches zero.

```cpp
scrollView.SetMaxFlingDistance(1000.0f);
scrollView.SetFlingSensitivity(1.5f);
scrollView.SetDecelerationRate(0.95f);
```

### Setting Duration Constraints
The `SetMinimumFlingDuration` and `SetMaximumFlingDuration` methods ensure that scroll animations stay within expected timing boundaries, providing consistent UX across different device performance profiles.

* **duration**: Time in milliseconds for the scroll animation to complete.

```cpp
scrollView.SetMinimumFlingDuration(200);
scrollView.SetMaximumFlingDuration(800);
```

## Handling Scroll and Drag Signals

Signals allow you to hook into the lifecycle of a gesture, providing opportunities to trigger loading logic or update UI indicators.

### Monitoring Interaction
ScrollView exposes signals for tracking both user-initiated drags and automated scroll movements. You can connect to `ScrollStartedSignal`, `ScrollingSignal`, or `ScrollFinishedSignal` to react to content displacement.

```cpp
// Example: Reacting to the start of a scroll
scrollView.ScrollStartedSignal().Connect([](const Dali::Ui::ScrollView& view) {
    // Logic for when scrolling begins
});

// Example: Tracking while in motion
scrollView.ScrollingSignal().Connect([](const Dali::Ui::ScrollView& view) {
    Vector2 pos = view.GetScrollPosition();
    // Update progress indicators
});
```

> Note: Signals are triggered frequently during high-speed movement. Keep logic inside these slots lightweight to avoid frame drops.

## Implementing Edge Effects

Edge effects provide visual feedback when a user attempts to scroll beyond the defined content boundaries.

### Setting Over-Scroll Mode
The `SetOverScrollMode` method defines how the `[ScrollView](./scroll-view.md)` behaves at the limits of its content area.

* **mode**: Uses `OverScrollMode` to toggle effects such as 'glow' or 'bounce' (or none).

```cpp
scrollView.SetOverScrollMode(Dali::Ui::OverScrollMode::Bounce);
```

## Managing Scroll Position

You can programmatically jump to specific points in your content or observe where the user currently is within the scrollable area.

### Getting and Setting Position
Use `GetScrollPosition` and `SetScrollPosition` to retrieve or modify the current offset of the content.

* **position**: A `Vector2` representing the X and Y coordinate of the top-left corner of the viewport relative to the content.

```cpp
// Move to specific coordinates immediately
scrollView.SetScrollPosition(Vector2(0.0f, 500.0f));

// Query current position
Vector2 current = scrollView.GetScrollPosition();
```

### Scrolling to Specific Elements
For more complex navigation, `ScrollTo` allows you to center or bring a specific child `View` into the current viewport.

* **child**: The `View` to navigate toward.
* **animation**: Boolean, set to `true` to animate the transition or `false` for an immediate jump.
* **scrollToPosition**: Controls the alignment behavior via `ScrollToPosition`.

```cpp
// Scroll to a specific child view with animation
Dali::Ui::View targetView = GetTargetView();
scrollView.ScrollTo(targetView, true, Dali::Ui::ScrollToPosition::MakeVisible);
```

> Warning: Always ensure the passed child is actually a descendant of the `[ScrollView](./scroll-view.md)` content to avoid unexpected behavior.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/scroll-view)
