---
id: animated-vector-image-visual
title: "AnimatedVectorImageVisual"
sidebar_label: "AnimatedVectorImageVisual"
---
## Introduction to Animated Vector Image Visual

The `animated-vector-image-visual` is a specialized DALi visual designed for [rendering](./rendering.md) resolution-independent, high-performance vector animations (typically Lottie/Bodymovin files). Unlike standard image [visuals](./visuals.md) that rely on raster buffers, this visual interprets vector data to produce crisp, scalable animations at any display density. You should use this visual when your application requires interactive, lightweight motion graphics or complex icons that need to remain sharp regardless of the zoom level or screen scale.

This visual is distinct due to its ability to manipulate specific vector properties at runtime through a defined key-path system, allowing for programmatic overrides of [animation](./animation.md) state without modifying the source file. It serves as the primary engine-level component for [rendering](./rendering.md) complex, time-based vector assets in the DALi framework.

## Property Configuration and Usage

The `animated-vector-image-visual` is configured by populating a `Property::Map` and applying it to an actor via the `VisualFactory`. The map requires specific keys to locate the asset and control its playback behavior.

### Property Definition

To create the visual, define the map with the `Toolkit::Visual::Property::TYPE` set to `Toolkit::Visual::ANIMATED_VECTOR_IMAGE`. The `URL` property should point to the vector [animation](./animation.md) file path.

```cpp
using namespace Dali;
Toolkit::VisualFactory visualFactory = Toolkit::VisualFactory::Get();
Property::Map visualMap;
visualMap[Toolkit::Visual::Property::TYPE] = Toolkit::Visual::ANIMATED_VECTOR_IMAGE;
visualMap[Toolkit::ImageVisual::Property::URL] = "animation.json";
visualMap[Toolkit::AnimatedVectorImageVisual::Property::LOOP_COUNT] = 0; // Infinite loop

// Apply to actor
Actor actor = Actor::New();
Toolkit::ImageView imageView = Toolkit::ImageView::New();
imageView.SetImage(visualMap);
actor.Add(imageView);
```

> Note: The `URL` must point to a supported vector format (typically JSON-based vector exports). Ensure the file path is accessible to the DALi resource loader.

## DevelAnimatedVectorImageVisual API Integration

The `DevelAnimatedVectorImageVisual` namespace provides advanced control over the animation lifecycle and dynamic property manipulation. This tier allows developers to target specific elements within the vector hierarchy for real-time updates.

### Dynamic Property Handling

The `DynamicPropertyInfo` struct is used to bind callbacks to specific vector paths within the animation, allowing you to intercept or modify properties dynamically.

```cpp
// Example: Setting up a dynamic property override
Dali::Ui::DevelAnimatedVectorImageVisual::DynamicPropertyInfo info;
info.id = 1001;
info.keyPath = "layers/icon_layer/transform/opacity";
info.property = Property::FLOAT;
// The callback is triggered when the engine reaches the defined keyPath during playback
info.callback = [](Dali::Property::Value& value) {
    value = 0.5f; // Override opacity to 50%
};
```

### Action Dispatching

Actions are performed via the `Visual::Base::DoAction` method, utilizing the `DevelAnimatedVectorImageVisual::Action::Type` enum to trigger events like play, pause, or jump to a specific frame.

```cpp
// Playing the animation programmatically
imageView.DoAction(Toolkit::Visual::Action::PLAY, Property::Value());

// Jumping to a specific frame (if supported by the action implementation)
imageView.DoAction(Toolkit::AnimatedVectorImageVisual::Action::JUMP_TO, Property::Value(30));
```

## Lifecycle and Resource Management

The `animated-vector-image-visual` manages resources via the DALi image-loading thread. When a visual is created, the animation data is parsed and cached internally to avoid redundant re-decoding.

1. **Initialization:** The resource is requested when the visual is attached to the scene.
2. **Buffering:** Vector data is processed into a representation compatible with the GPU backend.
3. **Deallocation:** When the actor or visual is destroyed, the internal cache is released, provided no other visuals are referencing the same asset.

> Warning: Large vector files with excessive paths can consume significant heap memory. Always reuse visual configurations for identical animations to leverage internal cache mechanisms.

## Signal Handling and Event Feedback

The visual communicates state changes and playback milestones through specific signals defined in `DevelAnimatedVectorImageVisual::Signal`. These allow the application to synchronize UI logic with the animation progress.

### Connecting to Signals

You can connect to signals emitted by the visual to react to events like `FINISHED` (when the animation completes its loop count).

```cpp
// Example: Connecting to the animation finished signal
imageView.ConnectSignal(slotDelegate, SIGNAL_FINISHED, &MyClass::OnAnimationFinished);
```

The signal types allow for reactive programming where the application flow is driven by the visual state, such as enabling a "Submit" button only after a "checkmark" animation has finished playing.

## Performance Considerations

To ensure smooth 60 FPS performance, keep the complexity of your vector files in check. 

* **Complexity Limits:** Avoid excessively high path counts or complex dynamic effects (e.g., recursive paths) that require intensive CPU per-frame recalculation.
* **Layer Caching:** If an animation contains static elements that do not move relative to each other, consider flattening those layers in the source file before import.
* **Thread Safety:** While `DoAction` and property changes are thread-safe within the DALi event thread, avoid heavy data processing inside the `DynamicPropertyInfo` callback, as this will block the [animation](./animation.md) [update](./update.md) loop.

→ See: [Visuals Overview] (General component documentation)

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animated-vector-image-visual)
