---
id: watch
title: "watch"
sidebar_label: "watch"
---
## Introduction to the Watch Module

The DALi Watch module provides a dedicated framework for developing time-keeping applications for wearable devices. It facilitates high-performance [rendering](./rendering.md) by integrating directly with system-level timing services, allowing developers to build responsive, power-efficient [watch](./watch.md) faces.

What makes this module distinct is its specialized handling of ambient versus interactive display states. By exposing event-driven [signals](./signals.md) for tick updates and mode transitions, the framework ensures that applications can minimize CPU and GPU activity while maintaining accurate time representation on always-on displays.

## Watch Application Architecture

The `WatchApplication` class serves as the fundamental entry point for all wearable applications, orchestrating the interaction between the DALi engine and the platform's wearable service. It extends the standard DALi application model to manage the specific lifecycle requirements of a [watch](./watch.md) face.

### Instantiating the Watch Application

The `WatchApplication` must be initialized at the start of the program to register the application with the platform's wearable infrastructure.

*   **New(int *argc, char **argv[])**: Initializes the application with command-line arguments. Use this if your application requires configuration from the system shell.
*   **New(int *argc, char **argv[], Dali::StringView stylesheet)**: Initializes the application with arguments and a custom stylesheet for themed [rendering](./rendering.md).

```cpp
#include <dali/devel-api/watch/watch-application.h>

int main(int argc, char** argv)
{
  // Initialize the watch application
  Dali::WatchApplication app = Dali::WatchApplication::New(&argc, &argv);
  
  // Enter the main loop
  app.MainLoop();
  return 0;
}
```

## Watch Lifecycle Management

Watch lifecycle management revolves around the transition between active and ambient modes, which is critical for power management on wearable hardware. Developers must ensure resources are released or throttled appropriately when the device enters ambient mode.

### Handling Mode Transitions
The `AmbientChangedSignal` notifies the application when the device switches state.

*   **AmbientChangedSignal()**: Returns a `WatchBoolSignal` that emits a boolean value (true for ambient, false for active).
*   **Usage**: Use this to hide complex animations or stop high-frequency updates when in ambient mode to conserve battery.

```cpp
// Example: Responding to ambient mode
app.AmbientChangedSignal().Connect([](bool isAmbient) {
  if(isAmbient) {
    // Disable high-frequency animations
  } else {
    // Resume high-frequency animations
  }
});
```

## Time Synchronization and Rendering

The `WatchTime` class provides the interface for retrieving current system time, including calendar information and specific time zone metadata. This class is designed to be queried inside signal callbacks to ensure the UI remains synchronized with the hardware tick.

### Accessing Time Data
`WatchTime` provides granular access to time components, allowing for both simple digital and complex analog watch face implementations.

*   **GetHour() / GetHour24()**: Returns the current hour.
*   **GetMinute() / GetSecond() / GetMillisecond()**: Returns the current minute, second, and millisecond respectively.
*   **GetUtcTime() / GetUtcTimeStamp()**: Provides standardized time formats for cross-region logic.

```cpp
void OnTick(Dali::WatchTime& time)
{
  int hour = time.GetHour();
  int minute = time.GetMinute();
  // Update your visual components here
}
```

> Note: For analog watch faces, ensure your rendering logic accounts for smooth transitions between seconds, or stick to integer-based second updates to minimize wake-ups.

## Thread Safety and Concurrency

The DALi Watch module operates on the principle of a single-threaded event loop. All `WatchTimeSignal` and `AmbientTickSignal` callbacks are executed on the main application thread where the DALi engine processes scene graph updates.

*   **Thread Safety**: You should not perform blocking I/O or heavy computation within the tick signals, as this will drop frames and cause the watch face to become unresponsive.
*   **Event Handling**: All signals are emitted within the context of the main loop. Modifying the scene graph (e.g., changing actor positions based on time) is safe within these signals because they are synchronous with the engine's update phase.

## Devel-API Integration Patterns

The `devel-api` provides hooks into low-level display properties, allowing developers to optimize rendering for specific hardware capabilities, such as circular displays or OLED burn-in protection.

*   **Dali::WatchTime(void *time_handle)**: A constructor provided by the integration layer that allows wrapping platform-specific native handles. This is primarily used by system-level plugins or advanced low-level extensions.

> Warning: Using internal `void*` handles from `devel-api` requires careful memory management and is generally intended only for integration-level developers creating custom platform wrappers.

## Efficient Resource Handling

Battery life is the primary constraint of any watch application. Always-on display (AOD) scenarios require that the GPU work be kept to an absolute minimum.

*   **Strategy**: Use `TimeTickSignal` for second-level updates and `AmbientTickSignal` for minute-level updates.
*   **Optimization**: 
    *   Avoid creating new objects or allocating memory inside the `TimeTickSignal` callback.
    *   Pre-allocate your visual actors and update only their `Property::POSITION` or `Property::ORIENTATION` in the tick callback.
    *   → See: [Dali::Actor] for properties manipulation.

```cpp
// Efficiently updating a clock hand
app.TimeTickSignal().Connect([](Dali::WatchTime& time) {
  float secondRotation = time.GetSecond() * 6.0f; // 360 degrees / 60 seconds
  mySecondHandActor.SetProperty(Dali::Actor::Property::ORIENTATION, 
                                Dali::Degree(secondRotation));
});
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/watch)
