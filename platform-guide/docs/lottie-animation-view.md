---
id: lottie-animation-view
title: "LottieAnimationView"
sidebar_label: "LottieAnimationView"
---
## Introduction to [LottieAnimationView](./lottie-animation-view.md)

The `LottieAnimationView` is a specialized variant of `ImageView` designed to render high-fidelity, resolution-independent vector animations defined in the Lottie JSON format. By offloading the complex [math](./math.md) of vector path interpolation to the DALi engine, it enables fluid, high-performance motion graphics without the resource overhead of frame-by-frame bitmaps.

Developers should use `LottieAnimationView` when the UI requires scalable, lightweight motion assets such as icons, loading indicators, or decorative transitions. It is distinct from standard `ImageView` components because it maintains an internal temporal state for playback, frame management, and loop iteration that is specifically tuned for vector-based keyframe data.

## Component Lifecycle and Initialization

`LottieAnimationView` instances are created via a static factory method that ensures proper internal resource initialization. Because it is a handle-based [object](./object.md), managing its lifetime involves understanding that the underlying implementation is managed by the DALi scene graph, while the handle remains a lightweight reference.

### Using the New() Factory Method
The `New()` method is the primary entry point for instantiation. It optionally accepts a string URL, which maps directly to the file system or a remote resource containing the Lottie JSON.

```cpp
#include <dali-ui/lottie-animation-view.h>

void CreateLottie(Dali::Ui::View parent) {
  // Create an instance and load the resource in one step
  auto lottieView = Dali::Ui::LottieAnimationView::New("assets/loading_spinner.json");
  
  // Attach to parent UI hierarchy
  parent.Add(lottieView);
}
```

### Resource Acquisition via SetResourceUrl
If an instance is created without an initial URL, or if the animation needs to be swapped dynamically at runtime, `SetResourceUrl` updates the internal asset state.

```cpp
void UpdateAnimation(Dali::Ui::LottieAnimationView lottieView) {
  // Update the animation source; the engine handles resource loading asynchronously
  lottieView.SetResourceUrl("assets/checkmark_success.json");
}
```

> Note: Changing the URL triggers a reload of the vector assets. This operation is asynchronous; the UI will update once the engine has parsed and cached the new Lottie data.

## Animation Control Interface

The `[LottieAnimationView](./lottie-animation-view.md)` exposes an explicit playback state machine that allows for granular control over the animation's timeline. These methods directly interface with the engine’s animation controller, ensuring that visual updates are synchronized with the frame refresh cycle.

### Play, Pause, and Stop
- `Play()`: Resumes or begins playback from the current time index.
- `Pause()`: Freezes the animation at its current frame without resetting the playhead.
- `Stop()`: Halts the animation and forces the playhead back to the initial (0%) frame.

```cpp
void TogglePlayback(Dali::Ui::LottieAnimationView lottieView, bool shouldPlay) {
  if (shouldPlay) {
    lottieView.Play();
  } else {
    lottieView.Pause();
  }
}

void ResetToStart(Dali::Ui::LottieAnimationView lottieView) {
  lottieView.Stop();
}
```

## Looping and Playback Configuration

Managing the iteration logic of an animation ensures that visual effects provide the expected feedback, such as infinite loading spinners or single-shot completion notifications.

### SetLoopCount and GetLoopCount
The loop count property determines how many times the sequence plays before settling on its final state. Setting a specific integer value allows for precise repetition, while the internal state keeps track of the iteration index.

```cpp
void ConfigureLooping(Dali::Ui::LottieAnimationView lottieView) {
  // Configure the view to loop 3 times
  lottieView.SetLoopCount(3);
  
  // Retrieve the value to verify state
  int currentLoops = lottieView.GetLoopCount();
}
```

> Warning: `SetLoopCount` does not imply infinite playback by default. Ensure your logic reflects the requirements of your specific UI interaction.

## Type Safety and Downcasting

Since the DALi UI hierarchy often treats elements as generic `View` types (e.g., when traversing children of a container), `DownCast` provides a safe way to recover a `[LottieAnimationView](./lottie-animation-view.md)` handle.

### Safely Casting from Base Handles
Always use `DownCast` to check if a generic handle is a `[LottieAnimationView](./lottie-animation-view.md)` before calling animation-specific methods.

```cpp
void ProcessView(Dali::Ui::View view) {
  Dali::Ui::LottieAnimationView lottie = Dali::Ui::LottieAnimationView::DownCast(view);
  if (lottie) {
    // Successfully cast; it is safe to control this view
    lottie.Play();
  }
}
```

## Thread Safety and Integration Considerations

DALi operates on a strict thread-affinity model where the UI thread is responsible for both the construction of UI objects and the modification of their render-graph properties.

- **Resource Loading**: While `SetResourceUrl` is called from the main thread, the engine handles the actual file I/O and parsing of Lottie JSON on background worker threads to prevent frame drops.
- **State Synchronization**: Calling `Play()`, `Pause()`, or `Stop()` must happen on the main UI thread. Calling these from background threads will result in undefined behavior.
- **Memory Management**: Like all DALi handles, `[LottieAnimationView](./lottie-animation-view.md)` uses intrusive reference counting. The underlying native [object](./object.md) will be destroyed by the engine once all handles are out of scope and the view has been removed from the scene graph.

→ See: [ImageView](image-view-documentation-link)

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/lottie-animation-view)
