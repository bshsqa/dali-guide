---
id: watch
title: "watch"
sidebar_label: "watch"
---
## Introduction to the Watch Feature

The DALi Watch module is a specialized framework designed for developing high-performance, power-efficient [watch](./watch.md) faces for wearable devices. By leveraging the core DALi scene graph, it provides a dedicated lifecycle and event-driven architecture specifically tuned for time-keeping applications.

Developers should use the Watch module when building native [watch](./watch.md) faces that require precise time tracking, system time synchronization, and optimized [rendering](./rendering.md) to maximize battery life. It is distinct from standard DALi applications by providing a specialized entry point (`WatchApplication`) and event hooks (`TimeTick`) that allow the system to suspend and resume the application process in alignment with display requirements.

## Initializing Watch Applications

Initializing a [watch](./watch.md) face requires shifting from a standard `Application` to a `WatchApplication` to ensure the platform correctly manages the [watch](./watch.md) face lifecycle. This setup provides the necessary hooks to handle the [watch](./watch.md)-specific startup sequence and scene graph management.

### WatchApplication Class
The `WatchApplication` class acts as the primary container for your [watch](./watch.md) face, managing the window and system [events](./events.md). It inherits from `Application`.

*   **WHAT:** It provides the entry point for the [watch](./watch.md) face and manages the application’s lifecycle within the wearable environment.
*   **WHY:** Use this to initialize the DALi environment specifically for a [watch](./watch.md) face, ensuring proper integration with the system's ambient and active modes.
*   **HOW:** Instantiate the class, typically passing the application arguments. Use the `Register` method to attach callbacks for system [events](./events.md).
*   **CODE:**

```cpp
#include <dali/dali.h>
#include <dali/devel-api/watch/watch-application.h>

int main(int argc, char** argv)
{
  // Initialize the watch application entry point
  Dali::WatchApplication watchApp = Dali::WatchApplication::New(argc, argv);
  
  // Set up the scene or add initial actors here
  // ...

  watchApp.MainLoop();
  return 0;
}
```

## Retrieving and Formatting Time

The `WatchTime` class provides a controlled interface to access the system clock and calendar data. This class abstracts the complexity of locale-aware time formatting and time zone management.

### WatchTime Class
`WatchTime` encapsulates current temporal data, allowing developers to query hours, minutes, seconds, and date information.

*   **WHAT:** It provides an interface to retrieve the current system time components.
*   **WHY:** Use this to update the visual position of clock hands or the text content of a digital time display.
*   **HOW:** Instantiate `WatchTime` to fetch the current state. Methods like `GetHour()`, `GetMinute()`, and `GetSecond()` return the respective integer values.
*   **CODE:**

```cpp
Dali::WatchTime time = Dali::WatchTime::New();
int hours = time.GetHour();
int minutes = time.GetMinute();
int seconds = time.GetSecond();

// Update UI elements based on time
hourHand.SetProperty(Dali::Actor::Property::ORIENTATION, Dali::Degree(hours * 30.0f));
```

## Handling Time-Tick Events

Time-tick events are the heartbeat of a watch face, signaling when the UI must be updated to reflect the passage of time. Efficiently responding to these events is critical for balancing responsiveness with power consumption.

### TimeTick Callback
The `TimeTick` event is triggered by the system to notify the watch face that the time has advanced.

*   **WHAT:** This callback occurs every minute (or more frequently, depending on the watch mode) to allow the UI to refresh.
*   **WHY:** Developers must use this to synchronize the UI state with the system clock, ensuring the watch face never shows stale time.
*   **HOW:** Connect to the `TimeTickSignal` emitted by the `WatchApplication`.
*   **CODE:**

```cpp
void OnTimeTick(Dali::WatchTime& time)
{
  // Update time-dependent UI components
  digitalClockText.SetProperty(Dali::TextActor::Property::TEXT, 
                                std::to_string(time.GetHour()) + ":" + 
                                std::to_string(time.GetMinute()));
}

// In initialization:
watchApp.TimeTickSignal().Connect(&OnTimeTick);
```

> Note: Updating the UI during a `TimeTick` event should be kept lightweight to prevent battery drain. Avoid heavy calculations or complex scene graph modifications within the callback.

## Optimizing Watch Face Performance

Battery longevity is the primary constraint of wearable software. By utilizing the `WatchApplication` lifecycle methods and minimizing redundant updates, developers can create highly efficient watch faces.

*   **WHAT:** Performance optimization involves disabling animations or complex renders during "Ambient Mode" and limiting the frame rate.
*   **WHY:** Wearable devices spend a significant amount of time in low-power states; improper usage will result in excessive battery depletion.
*   **HOW:** Monitor the state of the watch application and pause animations via `Actor` property changes when the device is not in the active state.
*   **CODE:**

```cpp
void OnAmbientModeChanged(bool isAmbient)
{
  if(isAmbient)
  {
    // Stop animations to save battery
    myAnimation.Stop();
    // Simplified rendering path
    secondsHand.SetProperty(Dali::Actor::Property::VISIBLE, false);
  }
  else
  {
    // Resume animations for active user interaction
    myAnimation.Play();
    secondsHand.SetProperty(Dali::Actor::Property::VISIBLE, true);
  }
}
```

## Common Watch Face Patterns

Common patterns involve combining basic DALi actors with the `WatchTime` data to create either classic analog faces or modern digital displays. 

### Creating an Analog Face
To create an analog face, map `WatchTime` values to the `ORIENTATION` property of rotationally symmetric actors (hands).

*   **WHAT:** Rotating actors around a central anchor point based on time increments.
*   **WHY:** This is the standard way to represent time on a traditional watch face.
*   **HOW:** Set the `ANCHOR_POINT` and `PARENT_ORIGIN` of the hand actors to the center of the screen, then apply rotation based on the current time.
*   **CODE:**

```cpp
// Set anchor to center for rotational hands
hourHand.SetProperty(Dali::Actor::Property::ANCHOR_POINT, Dali::AnchorPoint::CENTER);
hourHand.SetProperty(Dali::Actor::Property::PARENT_ORIGIN, Dali::ParentOrigin::CENTER);

// Calculate hour hand rotation (12 hours = 360 degrees, so 30 degrees per hour)
float rotation = time.GetHour() * 30.0f;
hourHand.SetProperty(Dali::Actor::Property::ORIENTATION, Dali::Degree(rotation));
```

→ See: [Actor], [Property]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/watch)
