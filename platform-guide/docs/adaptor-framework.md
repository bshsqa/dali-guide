---
id: adaptor-framework
title: "adaptor-framework"
sidebar_label: "adaptor-framework"
---
## Introduction to the Adaptor Framework

The `adaptor-framework` acts as the critical bridge between the hardware-accelerated DALi core engine and the host operating system's environment. It provides the necessary infrastructure to map high-level `Dali::Ui::View` components into platform-specific windowing systems, handle system-level [events](./events.md), and manage lifecycle transitions.

Developers should rely on this framework when they need to bridge the gap between their UI implementation and the underlying OS, such as managing application startup, interacting with system-level accessibility services, or executing asynchronous tasks that reside outside the main UI thread. It is distinct from the core engine by serving as the implementation layer that enables DALi to function as a first-class citizen on diverse platforms.

## Application Lifecycle and Initialization

The application lifecycle is managed through controllers that maintain the main loop and ensure that the UI tree remains synchronized with the platform's execution state. Proper initialization is required before any `Dali::Ui::View` is created or rendered.

→ See: [Windowing and Surface Management]

## Windowing and Surface Management

Windowing and surface management define how your application occupies space on the display. By interacting with window interfaces, developers can control the [rendering](./rendering.md) surface properties of their `Dali::Ui::View` hierarchies.

## Asynchronous Task Management

Asynchronous task management allows for the execution of long-running operations without blocking the [rendering](./rendering.md) thread, ensuring that `Dali::Ui::View` updates remain fluid. By utilizing the task manager, developers ensure that heavy computations or I/O operations do not stall the main UI loop.

## System Integration and External Communication

This module provides the necessary handles to integrate with system services like the clipboard, input methods, and battery monitoring. These bridges are essential for maintaining platform consistency and responding to environment-driven state changes.

## Accessibility Framework Implementation

The accessibility framework allows developers to expose `Dali::Ui::View` components to assistive technologies by implementing the `Dali::Accessibility::Accessible` interface. This ensures that UI elements can be queried, navigated, and manipulated by external accessibility services via the Atspi interface.

### The Accessible Interface
The `Dali::Accessibility::Accessible` class serves as the base contract for any UI component that requires accessibility support. By inheriting from this, your custom view components can provide names, descriptions, and structural information to the OS.

> **Note:** Always ensure the `GetInternalActor()` method returns a valid `Dali::Actor` handle if your accessibility component is backed by a UI element, as this allows the engine to map accessibility [events](./events.md) to actual spatial locations on the screen.

#### Example: Implementing an Accessible View
```cpp
#include <dali-toolkit/dali-toolkit.h>
#include <dali/devel-api/adaptor-framework/accessibility-bridge.h>

class MyAccessibleView : public Dali::Ui::View, public Dali::Accessibility::Accessible {
public:
    std::string GetName() const override {
        return "My Custom Button";
    }

    std::string GetDescription() const override {
        return "A button that performs a specific action.";
    }

    Dali::Actor GetInternalActor() const override {
        // Return the underlying actor used by the View
        return this->GetActor();
    }
    
    // ... Implement remaining virtual methods from Accessible interface
};
```

### Tree and Gesture Navigation
Accessibility services rely on the tree structure and spatial information provided by your implementation. Use `DumpTree` for debugging your accessibility hierarchy to ensure all child nodes are reachable.

*   **GetChildCount**: Returns the number of accessible children in the current node.
*   **GetAccessibleAtPoint**: Retrieves the `Accessible` object located at the specified screen coordinate, allowing for hit-testing by screen readers.

## Device and Media Plugins

Extensibility in the `[adaptor-framework](./adaptor-framework.md)` is achieved through a plugin architecture. These components allow the engine to load specific implementations for hardware-bound features like camera feeds or video playback without bloating the core framework.

## Inter-thread Event Handling

Inter-thread communication is vital for offloading logic from the main thread while maintaining thread safety when updating the UI. The framework provides safe dispatch mechanisms to ensure that [signals](./signals.md) originating from background threads are properly queued and executed on the main event loop.

→ See: [Asynchronous Task Management]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/adaptor-framework)
