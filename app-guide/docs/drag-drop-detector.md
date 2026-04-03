---
id: drag-drop-detector
title: "drag-drop-detector"
sidebar_label: "drag-drop-detector"
---
## Introduction to Drag and Drop Detector

The `DragAndDropDetector` is a specialized input handling component in the DALi framework designed to simplify the implementation of touch-based data transfer. It manages the lifecycle of a drag gesture, monitoring the user's touch points across the screen to identify when an [object](./object.md) is being grabbed, moved, and released onto a target area.

You should use this detector when your application requires interactive reordering, item movement between containers, or file transfers between UI elements. It is distinct from other gesture detectors because it provides built-in support for "payloads," allowing you to encapsulate and transmit data objects seamlessly from the source actor to a valid drop target.

## Configuring the Detector

To use the detector, you must instantiate it and attach it to a specific `Actor`. The detector will then listen for touch [events](./events.md) occurring within the bounds of that actor to initiate a drag sequence.

### Creating and Attaching the Detector

The `DragAndDropDetector::New()` factory method initializes a new detector, which must then be associated with an actor to define the draggable region.

*   **WHAT:** Creates a new instance of the `DragAndDropDetector`.
*   **WHY:** Necessary to begin the drag-and-drop lifecycle for a specific UI element.
*   **HOW:** Pass a pointer to the target `Actor` to the constructor (internally via the `New` method).
*   **CODE:**

```cpp
#include <dali/dali.h>
#include <dali/public-api/adaptor-framework/drag-and-drop-detector.h>

using namespace Dali;

void SetupDrag(Actor myDraggableActor)
{
  // Create the detector and attach it to the actor
  DragAndDropDetector detector = DragAndDropDetector::New(myDraggableActor);
}
```

> Note: If the actor is hidden or not added to the stage, the detector will not trigger events. Ensure your draggable actors are visible and touchable.

## Handling Drag Events

The `DragAndDropDetector` exposes a suite of signals that allow you to react to the different phases of a drag-and-drop operation. 

### Connecting to Signals

*   **WHAT:** Provides callbacks for `Started`, `Moved`, `Dropped`, and `Cancelled` states.
*   **WHY:** Allows the application to update its visual state (e.g., show a ghost image) or perform logic when a drop occurs.
*   **HOW:** Connect to `StartedSignal()`, `MovedSignal()`, `DroppedSignal()`, and `CancelledSignal()`.
*   **CODE:**

```cpp
void OnDragStarted(DragAndDropDetector& detector, const DragEvent& event)
{
  // Logic for when the drag starts
}

// Inside your initialization
detector.StartedSignal().Connect(&OnDragStarted);
```

## Managing Drag Payloads

Data transfer is managed through payloads, which contain the metadata or information being moved. The payload is typically defined at the start of the drag and retrieved by the target upon the drop event.

*   **WHAT:** Defines the data object attached to the drag operation.
*   **WHY:** To carry information from the source actor to the eventual drop location.
*   **HOW:** Use the `Payload` object provided within the `DragEvent` during the `Started` signal to set your data.
*   **CODE:**

```cpp
void OnDragStarted(DragAndDropDetector& detector, const DragEvent& event)
{
  // Set data to transfer
  event.payload.SetData("ItemID_123");
}
```

> Note: Payloads are platform-level details when interacting with system-wide drag-and-drop services. Refer to the platform guide for cross-application data sharing constraints.

## Visual Feedback Best Practices

Providing visual feedback is crucial for a responsive user experience. During the `Moved` signal, you should update the position of a "proxy" actor or ghost image to follow the user's touch.

*   **WHAT:** Synchronizing UI elements with touch coordinates.
*   **WHY:** To give the user a clear indication of what is being dragged and where it is currently positioned.
*   **HOW:** Extract the current screen coordinates from the `DragEvent` and update your actor's `Position` property.

```cpp
void OnDragMoved(DragAndDropDetector& detector, const DragEvent& event)
{
  // Update a ghost actor to follow the touch
  ghostActor.SetPosition(event.screenCoordinates);
}
```

## Common Patterns and Recipes

Standard patterns include list reordering or moving icons between folders. In these cases, maintain a reference to the source container and the target container.

*   **List Reordering:** Upon `DroppedSignal`, identify the target actor under the coordinate and re-insert the data payload into the new list index.
*   **Item Movement:** Use the `CancelledSignal` to reset your draggable actor to its original position if the user drops the item in a non-valid area.

> Warning: Always ensure that your signals are disconnected when the `DragAndDropDetector` or the associated actors are destroyed to prevent memory access violations or stale callback triggers.

→ See: `GestureDetector` for general touch event handling.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/drag-drop-detector)
