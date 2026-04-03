---
id: scroll-view
title: "ScrollView"
sidebar_label: "ScrollView"
---
## Introduction to [ScrollView](./scroll-view.md)

`Dali::Ui::ScrollView` is a specialized container component designed to manage content that exceeds the physical boundaries of the screen or its parent viewport. By providing automatic clipping, gesture recognition, and kinetic physics, it enables developers to implement smooth, performant scrolling experiences with minimal boilerplate code.

Use `ScrollView` when you need to display large lists, infinite content, or scrollable form interfaces. Unlike standard `Dali::Ui::View` components that rely on manual transformation or [animation](./animation.md) to change visual offsets, `ScrollView` encapsulates the state, touch-to-scroll logic, and physics-based inertia, making it the distinct choice for interactive content navigation.

## Content Management and Positioning

The `ScrollView` acts as a viewport; the content it displays must be explicitly defined to establish the scrollable bounds. By managing the `View` associated with the content, you define the target that will be transformed in response to input.

### Associating Content and Setting Positions
The `SetContent` method registers the `Dali::Ui::View` hierarchy that the scroll area will manipulate. You can also programmatically jump to specific coordinates using `SetScrollPosition` or transition smoothly using `ScrollTo`.

```cpp
using namespace Dali::Ui;

// Create the scroll container and content
ScrollView scrollView;
View contentContainer = View::New(); 

// Associate content
scrollView.SetContent(contentContainer);

// Programmatically set position
scrollView.SetScrollPosition(Vector2(0.0f, 500.0f));

// Smoothly scroll to a coordinate
scrollView.ScrollTo(Vector2(0.0f, 1000.0f), true);
```

> Note: `SetScrollPosition` moves the content immediately without animation. If you require a visual transition, always prefer `ScrollTo`.

## Configuring Scroll Behaviors

Fine-tuning the interaction model allows the `[ScrollView](./scroll-view.md)` to feel integrated with your application's design language. You can constrain motion to specific axes, determine how the container behaves when the user drags beyond the content bounds, and control visual feedback via scroll bars.

### Defining Direction and Constraints
Use `SetScrollDirection` to restrict movement to horizontal, vertical, or both axes. `SetOverScrollMode` defines the behavior when a drag exceeds the content limits, such as showing an edge effect or snapping back.

```cpp
// Restrict scrolling to vertical axis only
scrollView.SetScrollDirection(ScrollDirection::Vertical);

// Set over-scroll behavior (e.g., to bounce or clamp)
scrollView.SetOverScrollMode(OverScrollMode::Bounce);

// Configure scroll bar visibility
scrollView.SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto);
```

## Fling and Kinetic Physics

The "feel" of a scrollable interface depends heavily on how the view responds to the user's release gesture. `[ScrollView](./scroll-view.md)` provides an internal physics engine to handle deceleration and momentum, which can be tuned to match the expected performance of your UI.

### Tuning Kinetic Properties
Methods like `SetDecelerationRate`, `SetFlingSensitivity`, and `SetMaximumFlingDuration` allow you to define the friction and speed limits of the content movement after a user releases their finger. 

```cpp
// Make the fling more sensitive to flick gestures
scrollView.SetFlingSensitivity(1.5f);

// Increase friction to make the scroll stop faster
scrollView.SetDecelerationRate(0.95f);

// Constrain the fling animation duration (in milliseconds)
scrollView.SetMinimumFlingDuration(200);
scrollView.SetMaximumFlingDuration(1000);
```

> Warning: Setting extreme values for `SetFlingSensitivity` or `SetDecelerationRate` may lead to an unnatural user experience or jittery movement if the frame rate fluctuates.

## Event Handling and Signal Integration

`[ScrollView](./scroll-view.md)` exposes a robust signal interface that allows your application to react to the lifecycle of a gesture. Whether you are updating a progress indicator while dragging or loading lazy content when a scroll finishes, these signals are the primary entry point for logic.

### Subscribing to Interaction Signals
The `[ScrollView](./scroll-view.md)` provides signals for both the `Drag` state (the physical touch gesture) and the `Scroll` state (the resulting content transformation).

```cpp
// Subscribe to scroll start and finish signals
scrollView.ScrollStartedSignal().Connect([](ScrollView& view) {
    // Handle start of automated scrolling (e.g., hide floating buttons)
});

scrollView.ScrollFinishedSignal().Connect([](ScrollView& view) {
    // Handle completion of scrolling (e.g., trigger lazy data loading)
});

// Detect active dragging
scrollView.DraggingSignal().Connect([](ScrollView& view) {
    // Perform real-time UI updates while the user is actively touching the screen
});
```

## Integration Patterns for Engine Developers

For developers building custom components, `[ScrollView](./scroll-view.md)` provides mechanisms for thread-safe property access and state querying. By checking `IsScrolling()`, you can prevent race conditions in your custom `View` logic where multiple sources might attempt to modify the scroll offset simultaneously.

### Internal Synchronization and State
When implementing complex layouts, ensure that you query the `[ScrollView](./scroll-view.md)` state before triggering dependent animations. The engine ensures that `GetScrollPosition` and property updates are processed consistently with the `Render` loop, keeping the UI thread synchronized.

```cpp
void UpdateCustomComponent(ScrollView& scroll) {
    if(scroll.IsScrolling()) {
        // Pause resource-intensive operations while the scroll is active
        return;
    }
    
    // Proceed with state updates
    Vector2 currentPos = scroll.GetScrollPosition();
    // ...
}
```

> Note: `[ScrollView](./scroll-view.md)` manages its own internal [animation](./animation.md) timers. Avoid manually overriding the view's transformation matrix directly, as this will conflict with the internal physics engine's property updates.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/scroll-view)
