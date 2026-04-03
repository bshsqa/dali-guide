---
id: adaptor-framework
title: "adaptor-framework"
sidebar_label: "adaptor-framework"
---
## Introduction to the Adaptor Framework

The [adaptor-framework](./adaptor-framework.md) serves as the foundational bridge between the DALi UI engine and the underlying host platform. It provides the essential infrastructure required to initialize the application runtime, manage system [signals](./signals.md), and interface with platform-specific services.

Developers use this framework to define the entry point of their application, handle lifecycle transitions (such as pauses or low-memory [events](./events.md)), and obtain access to the main window surface where `Dali::Ui::View` hierarchies are rendered. It is distinct because it operates at the process level, abstracting platform complexities away from the high-level UI components.

## Managing Application Lifecycle

The application lifecycle is managed via the `Dali::Application` class, which serves as the primary controller for your program's execution flow. It provides the mechanisms to start the event loop and subscribe to system-wide notifications.

### Initializing and Running the Application
The `Application` class handles the instantiation of the environment required for `Dali::Ui::View` objects to exist. You must initialize an `Application` instance to start the main loop.

**Code Example:**

```cpp
#include <dali/dali.h>

int main(int argc, char **argv)
{
  Dali::Application app = Dali::Application::New(&argc, &argv);
  
  app.InitSignal().Connect([](Dali::Application& application) {
      // Application initialized, safe to create Dali::Ui::View objects here.
  });

  app.MainLoop();
  return 0;
}
```

### Handling Lifecycle Signals
The `Application` class exposes signals that allow you to react to OS-level events, such as low memory, orientation changes, or application suspension.

*   **InitSignal/TerminateSignal**: Triggered when the application starts or is requested to close.
*   **PauseSignal/ResumeSignal**: Used to manage state when the application moves to the background or foreground.
*   **LowMemorySignal**: Emitted when system resources are critical; use this to clear cache or release non-essential `Dali::Ui::View` content.

**Usage Note:** Connect to these signals during the `InitSignal` callback to ensure the application environment is fully prepared.

## Windowing and View Surface Configuration

The windowing component provides access to the visual surface where your UI is presented. While the `Adaptor` layer manages the low-level surface, your application interacts with it primarily through the `Application::GetWindow()` method.

### Configuring the Main Window
`Application::New` overloads allow you to define window properties such as opacity and initial size at the moment of creation.

**Code Example:**

```cpp
// Create an application with a specific window configuration
Dali::Application app = Dali::Application::New(
    &argc, &argv, 
    "stylesheet.css", 
    Dali::Application::WindowOpacity::OPAQUE
);

Dali::Window win = app.GetWindow();
// The win object is used to host your root Dali::Ui::View
```

> Warning: Always ensure the window is initialized before attempting to attach any `Dali::Ui::View` objects to the scene, as the view hierarchy requires a valid rendering surface.

## Handling System-Level Inputs and Feedback

The adaptor-framework provides hooks for global system events that affect how users interact with your views. This includes language and region changes, as well as orientation shifts.

### System Event Listeners
You can observe the environment using signals like `LanguageChangedSignal` or `DeviceOrientationChangedSignal`. When these fire, you may need to update the layout or localization strings within your `Dali::Ui::View` components.

**Code Example:**

```cpp
app.DeviceOrientationChangedSignal().Connect([](Dali::Application& app) {
    // Logic to re-orient or re-layout your main Dali::Ui::View
});
```

## Asynchronous Task Processing

Maintaining high frame rates is critical for smooth `Dali::Ui::View` animations. The `AddIdle` method allows you to offload non-blocking logic to the main loop, ensuring that heavy computations do not stall the UI thread.

### Scheduling Idle Tasks
Use `AddIdle` to register a callback that executes when the engine is not processing input or rendering frames.

**Code Example:**

```cpp
void MyTask() {
    // Perform light processing logic
}

// Inside your initialization
app.AddIdle(new Callback(&MyTask));
```

## Media and Content Loading Utilities

The adaptor-framework provides utilities to resolve resource paths, ensuring that your application can correctly locate assets (images, fonts, or localized files) regardless of the installation directory structure.

### Accessing Resources
Use `GetResourcePath()` to retrieve the root directory for your application assets.

**Code Example:**

```cpp
Dali::String path = app.GetResourcePath();
Dali::String imagePath = path + "images/logo.png";
// Use imagePath to set the source for your View assets
```

## Implementing Accessibility

Accessibility is critical for inclusive application design. The `ObjectRegistry` (accessible via `GetObjectRegistry()`) allows the application to manage object life-cycles and identify components to the screen reader framework. 

> Note: For detailed implementation of screen reader labels and focus navigation within your `Dali::Ui::View` components, refer to the platform-level accessibility guide, as these settings rely on property tagging that interacts with the `ObjectRegistry`.

## Drag and Drop Interactions

The adaptor-framework facilitates cross-view data transfers. While specific drag-and-drop start logic is handled by the interaction layer, the lifecycle of the drag operation is coordinated by the system defined in the adaptor. 

> Note: Drag and Drop functionality is a complex interaction pattern. Ensure that the source and target `Dali::Ui::View` objects have been added to the window hierarchy before initiating a drag sequence.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/adaptor-framework)
