---
id: dissolve-effect
title: "Dissolve Effect"
sidebar_label: "Dissolve Effect"
---
## Understanding the [Dissolve Effect](./dissolve-effect.md)

The `DissolveEffect` is a specialized visual shader component within the DALi framework designed to simulate the organic disappearance or appearance of UI elements. Unlike standard opacity-based transitions, the dissolve effect utilizes a noise-based texture map to govern the pixel-by-pixel removal of content, creating a "vaporizing" or "crystallizing" look.

You should use the `DissolveEffect` when you require more sophisticated, non-linear transitions for your interface elements, such as card reveals, status alerts, or stylized menu navigation. It is distinct from other effects because it leverages a procedural threshold mechanism to determine which fragments are rendered, providing a organic texture to the transition rather than a simple uniform fade.

→ See: [IrisEffect]

## Quick Start: Applying the Effect

To apply a dissolve effect, you create an instance of the effect and attach it to your desired `Actor` or `View` via the `ShaderEffect` container. This process establishes the link between the [rendering](./rendering.md) pipeline and the visual parameters that drive the effect.

### Attaching the Effect

The `DissolveEffect` is applied by adding it to an `Actor`. Once attached, you can manipulate the effect's properties to control the transition state.

```cpp
#include <dali-toolkit/dali-toolkit.h>

using namespace Dali;
using namespace Dali::Toolkit;

// Create an Actor to receive the effect
ImageView myImage = ImageView::New("my-content.png");
myImage.SetParentOrigin(ParentOrigin::CENTER);
Stage::GetCurrent().Add(myImage);

// Create the dissolve effect
// The effect uses the actor's size and provided noise map
DissolveEffect dissolve = DissolveEffect::New();

// Apply the effect to the actor
myImage.SetShaderEffect(dissolve);
```

## Configuring Dissolve Parameters

The dissolve effect is highly customizable, allowing you to control not just the progress, but the visual quality of the transition through noise mapping and edge smoothing.

### Adjusting Dissolve Properties

Control the effect's behavior by updating properties such as `progress`, `noiseTexture`, and `edgeHardness`.

*   **Progress**: A `float` representing the completion of the dissolve, ranging from `0.0` (fully visible) to `1.0` (fully dissolved).
*   **Noise Texture**: An `Image` or `Resource` path defining the pattern of the dissolve. A high-contrast texture generally yields a more pronounced, "jagged" dissolve.
*   **Edge Hardness**: A `float` defining the transition boundary width; lower values create a sharp, distinct edge, while higher values create a soft, blurred transition.

```cpp
// Set the progress to 50%
dissolve.SetProperty(DissolveEffect::Property::PROGRESS, 0.5f);

// Define how sharp the dissolve edge appears
dissolve.SetProperty(DissolveEffect::Property::EDGE_HARDNESS, 0.1f);
```

> Note: Ensure your noise texture is properly loaded into the resource cache before assignment to prevent frame drops during the initial transition.

## Animating Transitions

Because the `DissolveEffect` properties are registered as standard DALi properties, they are fully compatible with the DALi Animation system. This allows for smooth, hardware-accelerated transitions.

### Driving Progress with Animation

Use an `Animation` object to interpolate the `PROGRESS` property over a specific duration to create a smooth dissolve sequence.

```cpp
Animation animation = Animation::New(2.0f); // 2-second animation
animation.AnimateTo(Property(dissolve, DissolveEffect::Property::PROGRESS), 1.0f);
animation.Play();
```

## Handling Completion Signals

When an animation or sequence using the dissolve effect finishes, your application may need to trigger cleanup logic or follow-up UI states.

### Using Signal Callbacks

The `Animation` object provides the `FinishedSignal` which you should use to detect the end of the dissolve transition.

```cpp
animation.FinishedSignal().Connect([](Animation& source) {
  // Logic to execute after the element has fully dissolved
  // e.g., Set visibility to false or remove from Stage
});
```

## Performance and Resource Optimization

While the `DissolveEffect` is efficient, rendering complex shaders over large portions of the screen can impact performance on lower-end hardware.

### Optimization Guidelines

*   **Texture Size**: Keep your noise textures at the smallest resolution required to achieve the desired visual fidelity; 256x256 is usually sufficient for most UI elements.
*   **Overdraw**: Avoid stacking multiple actors with active dissolve effects, as each shader instance requires a separate draw call and fragment processing.
*   **Platform-level detail**: For highly specialized custom noise generation, consider using a `[FrameBuffer](./frame-buffer.md)` as a source if the dissolve pattern needs to be generated at runtime. See the platform-level API guide for texture buffer management.

> Warning: Excessive use of the `EDGE_HARDNESS` parameter in conjunction with very high-resolution noise textures can increase fragment shader complexity, potentially causing performance bottlenecks on mobile platforms. Always test your transition with standard GPU profiling tools.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/dissolve-effect)
