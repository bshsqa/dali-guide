---
id: alpha-discard-effect
title: "Alpha Discard Effect"
sidebar_label: "Alpha Discard Effect"
---
## Introduction to [Alpha Discard Effect](./alpha-discard-effect.md)

The `AlphaDiscardEffect` is a specialized shader effect within the DALi framework designed to provide precise control over fragment transparency by programmatically discarding pixels that fall below a specified alpha threshold. Unlike standard alpha-blending, which blends colors based on opacity, this effect performs an explicit discard operation, effectively creating "hard" cut-outs or silhouettes from textures or geometry without requiring complex geometry-level clipping.

Developers should utilize the `AlphaDiscardEffect` when implementing high-performance stencil masks, transparent edge optimization, or scenarios where standard interpolation is undesired and pixels should be treated as strictly opaque or fully discarded. This mechanism is distinct because it operates directly on the fragment shader's discard instruction, providing a cleaner cut than traditional alpha-test techniques. → See: [AlphaMaskEffect]

## Integration and Setup

Integrating the `AlphaDiscardEffect` requires attaching the effect instance to a DALi `Actor` to modify its [rendering](./rendering.md) behavior. This process ensures the fragment shader correctly intercepts the texture sampling process and applies the threshold logic before final color output.

### Instantiation and Attachment
To use the effect, you must instantiate the class and register it as a shader effect on the target actor. This requires a standard DALi `Actor` and the `Effect` interface.

```cpp
#include <dali/public-api/dali-core.h>
#include <dali/devel-api/shader-effects/alpha-discard-effect.h>

void SetupDiscardEffect(Dali::Actor actor)
{
  // Instantiate the effect
  auto effect = Dali::AlphaDiscardEffect::New();
  
  // Apply to the actor's renderer
  actor.SetShaderEffect(effect);
}
```

> Note: Ensure that the `Actor` has a valid `[Renderer](./renderer.md)` initialized; otherwise, the shader effect will not be correctly bound to the pipeline.

## Configurable Alpha Thresholds

The core utility of this effect lies in its ability to dynamically tune the discard threshold at runtime. This property defines the cutoff point: any fragment with an alpha value lower than the configured threshold will be discarded by the GPU, preventing it from writing to the frame buffer or depth buffer.

### Setting the Threshold
The `SetThreshold` method (or equivalent uniform setter) updates the `uDiscardThreshold` uniform within the internal GLSL shader.

*   **WHAT**: Sets the floating-point threshold value for the alpha discard operation.
*   **WHY**: This allows for dynamic animations, such as fading out an object by incrementally increasing the threshold, or masking parts of an image based on grayscale input.
*   **HOW**: The threshold should be a `float` between 0.0 (nothing discarded) and 1.0 (everything discarded).

```cpp
void UpdateThreshold(Dali::AlphaDiscardEffect effect, float value)
{
  // Apply a threshold to discard pixels below 0.5 alpha
  effect.SetThreshold(value);
}
```

## Internal Rendering Pipeline Integration

The `AlphaDiscardEffect` interacts with the DALi render task by modifying the fragment shader's output state. By utilizing the `discard` keyword in the underlying GLSL, the framework bypasses the standard write-to-buffer process for rejected pixels.

### Depth Testing and Early-Z
Because the `discard` instruction is executed in the fragment shader, it has significant implications for the rendering pipeline.

*   **Early-Z Implications**: When `discard` is used, the hardware must often disable Early-Z optimizations because the GPU cannot guarantee the fragment will be written to the buffer until the shader finishes execution.
*   **Depth Buffers**: Fragments that are discarded do not write to the depth buffer. This behavior is ideal for alpha-tested transparency, as it prevents transparent pixels from occluding objects behind them.

## Performance Considerations

While `AlphaDiscardEffect` is efficient for creating transparency, it carries specific performance overheads on mobile GPUs. Because the `discard` keyword can break Hierarchical Z (Hi-Z) optimizations, frequent use of this effect on large, overlapping textures can lead to overdraw bottlenecks and increased fragment shader load.

> Warning: Avoid using this effect on every single object in a complex scene. If an object is not transparent, prefer standard opaque shaders to maintain optimal depth-testing performance.

## Best Practices and Use Cases

To extract the most value from `AlphaDiscardEffect`, treat it as a surgical tool for visual transitions and masking rather than a general-purpose transparency solution.

### Implementing a Soft-Edge Transition
By animating the `Threshold` property over time, you can create a "dissolve" effect where an object fades out based on the alpha channel of its texture.

```cpp
// Example: Animating a dissolve transition
auto animation = Dali::Animation::New(2.0f);
animation.AnimateTo(Property(effect, AlphaDiscardEffect::Property::THRESHOLD), 1.0f);
animation.Play();
```

*   **Alpha Testing Textures**: Useful for [rendering](./rendering.md) leaves or fence-like textures where binary transparency (fully opaque or fully transparent) is required.
*   **Mask Transitions**: Apply the effect to a flat quad to create complex reveal animations for UI elements, effectively using the threshold to "carve" the image out of thin air.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/alpha-discard-effect)
