---
id: shader-effects
title: "shader-effects"
sidebar_label: "shader-effects"
---
## Introduction to [shader-effects](./shader-effects.md)

The `shader-effects` framework in DALi provides a high-level abstraction layer for applying complex visual transformations to graphical components. By encapsulating GLSL shader logic within specialized classes, it allows developers to implement advanced [rendering](./rendering.md) techniques—such as masking, procedural [animation](./animation.md), and optical distortion—without manually managing raw shader source code or uniform buffers.

Use this framework when your application requires non-standard visual processing that exceeds the capabilities of standard DALi materials. It is distinct from manual shader authoring because it provides a stable, property-based API that integrates natively with DALi's [animation](./animation.md) system, ensuring consistent behavior across different hardware platforms.

## Internal Architecture and Lifecycle

The `shader-effects` module acts as a bridge between the DALi `Visual` and the underlying `Shader` objects in the [rendering](./rendering.md) pipeline. Each effect registers specific properties as uniforms, which the DALi render thread consumes during the frame composition stage.

When an effect is applied to a `View` or `Actor`, the framework creates the necessary `Shader` [object](./object.md) and manages the binding of uniform variables. The lifecycle of an effect is tied to the parent [object](./object.md) it modifies; when the host [object](./object.md) is destroyed or the effect is removed, the associated GLSL resources are released, preventing memory leaks in the GPU context.

## Threading and Performance Considerations

Effect properties are exposed through DALi’s `Property` system, allowing for seamless updates from the application thread. While property value changes are thread-safe (handled via command queues to the render thread), developers must be mindful that complex fragment shaders can lead to significant GPU load, particularly on lower-end hardware.

> Note: Frequent updates to shader properties (e.g., in a frame-by-frame loop) should be performed using `Animation` objects rather than manual setter calls to ensure smooth synchronization with the VSync signal and to minimize thread-crossing overhead.

## Sub-Components Overview

The `shader-effects` family provides a library of pre-built, optimized effects for [common](./common.md) visual tasks.

* **[Alpha Discard Effect](./alpha-discard-effect.md)**: Provides efficient pixel discarding based on an alpha threshold, useful for optimized masking. → See: `AlphaDiscardEffect`
* **[Dissolve Effect](./dissolve-effect.md)**: Enables transition animations where pixels disappear based on a noise map or threshold property. → See: `DissolveEffect`
* **[Distance Field Effect](./distance-field-effect.md)**: Renders smooth [text](./text.md) or shapes from distance field textures, ensuring high-quality scaling. → See: `DistanceFieldEffect`
* **[Image Region Effect](./image-region-effect.md)**: Allows for the [rendering](./rendering.md) of specific sub-regions of an image, facilitating sprite sheet animations. → See: `ImageRegionEffect`
* **[Motion Blur Effect](./motion-blur-effect.md)**: Applies a directional blur simulation based on the velocity of an actor. → See: `MotionBlurEffect`
* **Motion Stretch Effect**: Distorts a texture along a vector to simulate high-speed movement. → See: `MotionStretchEffect`

## Integration API Usage

Applying an effect involves configuring the target `View` to accept the effect’s property set. You interact with these effects primarily by modifying their registered properties, which DALi automatically maps to shader uniforms.

### Example: Applying an Effect

The following example demonstrates how to initialize an effect and attach it to a component.

```cpp
#include <dali-toolkit/dali-toolkit.h>
#include <dali-toolkit/devel-api/shader-effects/dissolve-effect.h>

using namespace Dali;
using namespace Dali::Toolkit;

void ApplyDissolveEffect(Actor targetActor)
{
  // Create the effect object
  DissolveEffect effect = DissolveEffect::New();

  // Configure effect properties
  // The 'amount' property controls the progress of the dissolve
  effect.SetProperty(DissolveEffect::Property::AMOUNT, 0.5f);

  // Apply the effect to the target View (assuming targetActor contains a Visual)
  // The internal implementation maps these properties to shader uniforms
  targetActor.SetProperty(DevelActor::Property::SHADER_EFFECT, effect);
}
```

> Warning: When chaining multiple effects, ensure that the shader complexity does not exceed the hardware's maximum uniform vector limit, as this will lead to [rendering](./rendering.md) errors or fallback to fixed-function paths if supported. Always profile your shader-based animations on the target hardware to monitor frame time impact.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/shader-effects)
