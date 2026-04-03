---
id: lottie-animation-view
title: "LottieAnimationView"
sidebar_label: "LottieAnimationView"
---
## Introduction to [LottieAnimationView](./lottie-animation-view.md)

The `LottieAnimationView` is a specialized `View` component designed to render high-quality, resolution-independent vector animations exported as Lottie files. By utilizing vector data instead of pixel-based image sequences, this view ensures smooth playback and scalability across different screen densities.

You should use `LottieAnimationView` whenever your application requires complex motion graphics, such as interactive loading spinners, micro-interactions, or animated icons. It is distinct from standard `ImageView` components because it provides dedicated playback controls—like play, pause, and looping logic—tailored specifically for the Lottie [animation](./animation.md) lifecycle.

→ See: [[ImageView](./static-image-view.md)]

## Creating and Initializing

To integrate a Lottie [animation](./animation.md) into your UI, you must create an instance of `LottieAnimationView`. This is typically achieved using the static `New()` factory method, which manages the necessary underlying resources.

### New
The `New()` method creates an initialized `LottieAnimationView` handle. It accepts an optional `url` parameter, allowing you to define the source file during construction.

```cpp
#include <dali-ui/dali-ui.h>

// Creating a LottieAnimationView with a resource URL
Dali::Ui::LottieAnimationView myLottie = Dali::Ui::LottieAnimationView::New("my_animation.json");

// Adding it to the stage (assuming root view is available)
rootView.Add(myLottie);
```

> Note: Always prefer `[LottieAnimationView](./lottie-animation-view.md)::New()` over default construction when you need an active, ready-to-use component. An uninitialized handle (created via the default constructor) does not reference a valid UI object and will not render if added to the scene.

## Loading and Managing Animation Resources

Managing animation resources involves setting or updating the file source that the view renders. This allows you to swap animations dynamically based on application state.

### SetResourceUrl
The `SetResourceUrl` method updates the animation source. This is used when you need to change the animation content at runtime without destroying the view instance.

```cpp
Dali::Ui::LottieAnimationView lottie = Dali::Ui::LottieAnimationView::New();
lottie.SetResourceUrl("status_success.json");
rootView.Add(lottie);

// Swapping to a different animation later
lottie.SetResourceUrl("status_error.json");
```

The method takes a `Dali::String` representing the file path and returns a reference to the `[LottieAnimationView](./lottie-animation-view.md)`, enabling method chaining for configuration.

## Playback Control

Playback control is the core functionality that distinguishes `[LottieAnimationView](./lottie-animation-view.md)` from static content views. You can precisely control the runtime state of the animation via simple commands.

### Play, Pause, and Stop
- **Play()**: Starts or resumes the animation from its current frame.
- **Pause()**: Freezes the animation at the current frame.
- **Stop()**: Ends the playback and resets the animation to the first frame.

```cpp
Dali::Ui::LottieAnimationView animation = Dali::Ui::LottieAnimationView::New("loader.json");

// Start playing
animation.Play();

// Pause upon user interaction
void OnButtonPressed() {
    animation.Pause();
}

// Reset to start
void ResetAnimation() {
    animation.Stop();
}
```

## Looping and Playback Settings

In many UI scenarios, you may want an animation to repeat a specific number of times or run indefinitely. `[LottieAnimationView](./lottie-animation-view.md)` exposes loop management to handle these requirements easily.

### SetLoopCount and GetLoopCount
`SetLoopCount(int count)` defines how many times the animation cycles. `GetLoopCount()` allows you to retrieve the current setting to verify the playback behavior.

```cpp
Dali::Ui::LottieAnimationView pulseAnim = Dali::Ui::LottieAnimationView::New("pulse.json");

// Set to loop 3 times
pulseAnim.SetLoopCount(3);

// Verify loop count
int loops = pulseAnim.GetLoopCount();
```

> Note: Passing a value of 0 or a negative value typically denotes infinite looping in most Lottie-based frameworks; verify your specific asset requirement to ensure the intended playback cycle is achieved.

## Handling Type Safety with DownCast

When working with containers or event handlers that return a generic `BaseHandle` or a generic `View`, you must convert the object back to its specific `[LottieAnimationView](./lottie-animation-view.md)` type to access its unique methods.

### DownCast
The `DownCast` utility validates the handle type and allows you to safely access `[LottieAnimationView](./lottie-animation-view.md)` methods.

```cpp
Dali::BaseHandle handle = GetAnimationHandleFromContainer();

// Safe conversion to LottieAnimationView
Dali::Ui::LottieAnimationView lottie = Dali::Ui::LottieAnimationView::DownCast(handle);

if(lottie)
{
    // It is safe to use lottie methods now
    lottie.Play();
}
```

> Warning: Always check the returned handle (e.g., `if(lottie)`) after a `DownCast`. If the handle does not point to a `[LottieAnimationView](./lottie-animation-view.md)`, the result will be an empty handle, and calling methods on it will result in undefined behavior.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/lottie-animation-view)
