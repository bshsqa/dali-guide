---
id: drag-drop-detector
title: "drag-drop-detector"
sidebar_label: "drag-drop-detector"
---
## Introduction to DragAndDropDetector

The `DragAndDropDetector` is a specialized component within the DALi framework designed to manage and track cross-view drag-and-drop operations. It provides a robust event-driven pipeline that enables developers to monitor the lifecycle of a dragged [object](./object.md) as it enters, traverses, and is dropped onto specific interactive UI elements.

Unlike standard gesture detectors that focus on localized touch interactions, the `DragAndDropDetector` is distinct in its ability to associate external content payloads with spatial coordinates across the global view hierarchy. You should use this detector whenever your application requires complex inter-view data exchange, such as moving items between lists or performing visual drag-and-drop operations.

## Core API and Integration

The `DragAndDropDetector` class serves as the primary controller for managing drag interaction observers. It acts as an orchestrator that maps specific DALi `View` objects to drag-state [signals](./signals.md).

### Creating and Attaching the Detector

To initiate the detector, use the static `New()` method. Once created, you must register the specific views that should be capable of reacting to drag [events](./events.md) using the `Attach()` method.

```cpp
#include <dali/ui/drag-and-drop-detector.h>

void SetupDragSupport(Dali::Ui::View myView) {
    // Create the detector instance
    auto detector = Dali::Ui::DragAndDropDetector::New();
    
    // Attach a target view to begin monitoring its interaction state
    detector.Attach(myView);
}
```

- `New()`: Initializes a new detector instance; must be called to allocate the underlying engine resource.
- `Attach(View view)`: Registers a `View` to the detector so it can receive enter/exit/drop notifications.
- `Detach(View view)`: Removes a previously attached view to cease event tracking for that specific element.
- `DetachAll()`: Clears all current associations, useful during view hierarchy teardown.

## Detector Lifecycle and Threading

The `DragAndDropDetector` follows the standard DALi handle-based lifecycle, where the object manages a reference-counted pointer to the internal engine implementation.

### Threading Constraints
DALi’s event processing, including gesture and drag-drop detection, runs strictly on the **Main (UI) Thread**. Consequently, all detector methods—including attaching views and registering signals—must be invoked from the main thread. Accessing `DragAndDropDetector` instance members from worker threads is not thread-safe and will lead to undefined behavior in the event pipeline.

> Warning: Always ensure that signals are connected before a drag sequence begins. Modifying the list of attached views during an active drag operation may cause skipped event callbacks.

## Signal Handling and Event Propagation

The detector exposes a set of signals that allow your application to react to specific phases of the drag sequence. All signals utilize the `DragAndDropSignal` type, providing a unified interface for state machine transitions.

### Connecting to Signals

You can subscribe to these signals to update your UI (e.g., highlighting a drop zone when an object enters).

```cpp
void OnDragEntered(Dali::Ui::DragAndDropDetector& detector) {
    // Logic for visual feedback when a drag enters a view
}

// Connecting to the EnteredSignal
detector.EnteredSignal().Connect(&OnDragEntered);
```

- `StartedSignal()`: Emitted when a drag sequence is initiated.
- `EnteredSignal()`: Triggered when the dragged pointer/content enters the bounds of an attached view.
- `MovedSignal()`: Fired continuously as the drag payload traverses the attached view.
- `ExitedSignal()`: Triggered when the dragged content leaves the view boundary.
- `DroppedSignal()`: Fired when the drag sequence is completed by releasing the input within an attached view.
- `EndedSignal()`: Emitted when the entire drag sequence concludes, regardless of where it occurred.

## Integration API for Engine Developers

The `devel-api` allows for deeper hooks into the windowing system and raw input stream processing. It provides the low-level capability to bridge platform-specific native drag events with DALi’s internal representation.

### Accessing State and Content
For developers extending the engine or implementing custom window managers, the following methods are essential for inspecting the current drag state.

```cpp
void InspectDragState(const Dali::Ui::DragAndDropDetector& detector) {
    // Retrieve the payload string
    const std::string& content = detector.GetContent();
    
    // Retrieve coordinates in screen space
    const Dali::Vector2& pos = detector.GetCurrentScreenPosition();
}
```

- `GetContent()`: Returns the string representation of the data currently being dragged; useful for content-aware drop validation.
- `GetCurrentScreenPosition()`: Returns the current `Vector2` screen coordinates of the drag cursor, critical for implementing custom collision detection logic or visual drag-shadows.

## Troubleshooting and Debugging

Debugging drag-and-drop systems often involves identifying why events are failing to trigger. Common pitfalls include improper view bounds or z-order occlusion.

### Common Pitfalls
1. **Z-Order/Occlusion**: If a view is obscured by an invisible or transparent parent, the detector may fail to trigger `EnteredSignal`. Ensure your view hierarchy is correctly layered.
2. **Handle Nullity**: Always verify that the `DragAndDropDetector` handle is valid before use, especially if the detector was destroyed due to application lifecycle transitions.
3. **Event Latency**: If signals are not firing, check the `GetAttachedViewCount()` to verify your `Attach()` calls were successful and that the views are not being detached prematurely by a parent component lifecycle event.

> Note: Use `GetAttachedView(index)` to iterate through current registered views if you need to perform a diagnostic audit of which components are currently active within your detector instance.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/drag-drop-detector)
