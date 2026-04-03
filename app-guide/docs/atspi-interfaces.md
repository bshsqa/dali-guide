---
id: atspi-interfaces
title: "atspi-interfaces"
sidebar_label: "atspi-interfaces"
---
## Introduction to AT-SPI Interfaces

The `atspi-interfaces` module provides the framework-level implementation for Assistive Technology Service Provider Interface (AT-SPI) standards within DALi. By implementing these interfaces, developers ensure their UI components are correctly interpreted by screen readers and other assistive tools, making applications accessible to users with diverse needs.

This module is distinct because it bridges the gap between DALi’s high-performance scene graph and the OS-level accessibility daemon. You should use these interfaces whenever you create custom UI components that require interaction beyond standard system controls, ensuring that state, labels, and hierarchical data are exposed correctly to the accessibility tree.

## The Accessible Base Interface

The `Accessible` interface acts as the foundation for all accessibility-enabled components in DALi. It provides the mechanism for mapping visual nodes to their corresponding accessibility metadata.

### Implementing Accessibility
To expose a component, you associate it with an implementation of the `Accessible` interface. This allows the system to query the role, description, and parent-child relationships of your UI element.

> Note: Implementation details regarding specific role assignments for custom widgets involve platform-level detail. Please refer to the platform guide for specific role constants.

## Interacting with UI Elements

The `Action`, `Component`, and `Value` interfaces allow assistive technologies to perform simulated user inputs and query the physical or logical status of a UI element.

### Component and Action Interfaces
The `Component` interface provides information about the geometric bounds of an element, while the `Action` interface allows for triggering [events](./events.md) such as "click" or "toggle" programmatically via accessibility tools.

## Managing Text and Hypertext Content

These interfaces ensure that [text](./text.md)-based components, such as entry fields or formatted labels, are navigable by screen readers.

### Configuring Text and EditableText
The `Text` interface exposes static content, while `EditableText` provides the necessary hooks for caret positioning, [text](./text.md) selection, and modification by assistive services.

## Advanced Interaction Patterns

For complex [layouts](./layouts.md) or lists, the `Collection` and `Selection` interfaces allow screen readers to treat groups of objects as single navigable units or logically associated lists.

### Implementing Collection
By using the `Collection` interface, you allow assistive technologies to perform efficient tree traversal, skipping unnecessary nodes to focus on items that the user can actually interact with or gather information from.

## Application-Level Accessibility

The `Application` class acts as the root of the accessibility hierarchy. It manages the lifecycle and ensures that the application is correctly registered with the system's accessibility daemon.

### Initializing the Application Context
The `Dali::Application` class is the mandatory entry point for every DALi application. It manages the connection between your application’s event loop and the system services, including accessibility registration.

```cpp
#include <dali/public-api/dali-application/application.h>

int main(int argc, char **argv[])
{
  // Create the application object to initialize the environment
  Dali::Application app = Dali::Application::New(&argc, &argv);

  // The Application object registers the process with the system.
  // It handles the main loop which coordinates accessibility signals.
  app.MainLoop();

  return 0;
}
```

> Note: Always ensure `MainLoop()` is called to allow the framework to process accessibility requests sent from external system daemons.

## Best Practices for Accessibility Compliance

To ensure your application is fully compliant and user-friendly, combine the interfaces logically. Always provide a clear, localized name for every `Accessible` object.

### Implementation Checklist
- **Hierarchy:** Ensure your accessibility tree reflects the logical structure of your UI.
- **Feedback:** Use `Action` interfaces to confirm interactions so that screen readers can provide immediate audio feedback.
- **Consistency:** Use consistent roles for similar UI patterns (e.g., all buttons should share the same role).

> Warning: Failure to implement the `Accessible` interface for custom complex controls will result in those elements being invisible to screen readers, [rendering](./rendering.md) them inaccessible.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/atspi-interfaces)
