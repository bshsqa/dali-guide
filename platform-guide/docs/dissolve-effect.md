---
id: dissolve-effect
title: "Dissolve Effect"
sidebar_label: "Dissolve Effect"
---
## [Dissolve Effect](./dissolve-effect.md) Overview

The `DissolveEffect` is a specialized shader-based transition mechanism designed to perform alpha-discard masking on textures, creating a "melting" or "disappearing" aesthetic. Unlike standard alpha-blending, which modulates pixel opacity, this effect utilizes a noise-based texture map to selectively discard fragments, making it ideal for high-performance scene transitions, UI component arrivals, or dynamic content removal without the overhead of heavy transparency sorting.

It is distinct from other shader effects due to its reliance on a high-frequency noise texture and a single-channel threshold parameter. When you require a non-linear, granular transition that reveals or hides content based on arbitrary shapes defined in a noise map, `DissolveEffect` is the preferred tool. → See: [ShaderEffects](https://docs.tizen.org/application/native/api/wearable/latest/group__dali__toolkit__shader__effects.html)

## Dissolve Properties and Uniforms

This effect exposes specific properties that map to the underlying GLSL uniforms, allowing real-time control over the transition's progression and visual characteristics.

### Controlling Transition Parameters

The `DissolveEffect` relies on key uniform properties to define the state of the transition.

* **uDissolveProgress**: A `float` representing the current state of the transition (0.0 to 1.0).
* **uDissolveThreshold**: A `float` defining the cutoff intensity; fragments with a noise value below this are discarded.
* **uDissolveEdgeWidth**: A `float` controlling the width of the burning/dissolve edge color.

> **Note:** The `uDissolveProgress` should be animated using the `DALi::Animation` class to ensure smooth frame-by-frame updates, as manually updating these values in a tight loop may introduce micro-stutter if not synchronized with the display refresh rate.

```cpp
// Example: Setting dissolve properties on an actor
Actor myActor = Actor::New();
// Assuming effect is initialized via the toolkit helper
Property::Map effectMap = CreateDissolveEffect();
myActor.SetProperty(Actor::Property::SHADER, effectMap);

// Set properties via the actor's custom properties
myActor.RegisterProperty("uDissolveProgress", 0.0f);
myActor.RegisterProperty("uDissolveThreshold", 0.2f);
```

## Integration with Actor Hierarchy

Integrating `DissolveEffect` into the actor hierarchy involves applying the shader to a `[Renderer](./renderer.md)` or attaching it to an actor through the property system. 

The lifecycle of the `DissolveEffect` is bound to the `[Renderer](./renderer.md)` attached to the actor. When the actor is removed from the scene, the shader uniform buffers are marked for cleanup by the render-thread, provided no other renderers share the same shader instance. To ensure resources are released correctly, avoid manual destruction of the shader and instead rely on the object's reference counting.

```cpp
// Example: Applying to a Renderer
Renderer renderer = Renderer::New(geometry, shader);
renderer.RegisterProperty("uDissolveProgress", 0.0f);

Actor actor = Actor::New();
actor.AddRenderer(renderer);
Stage::GetCurrent().Add(actor);
```

## Rendering Pipeline and Performance

The `DissolveEffect` operates by utilizing a fragment shader's `discard` keyword. While this avoids the overhead of blending, it can trigger early-Z failure or impact the efficiency of certain tile-based rendering (TBR) architectures.

* **Thread Safety**: Property updates are thread-safe and are queued for the next frame's render pass. 
* **Uniform Buffer Updates**: Updating properties frequently is efficient, but avoid excessive calls per-frame; update only when the transition state changes.
* **Performance Impact**: Use `discard` sparingly on large screens, as it can disable early-Z optimization on specific GPU architectures.

> **Warning:** Excessive reliance on `discard` can cause performance degradation on low-end hardware. Always ensure that the noise map used for the dissolve effect has a power-of-two resolution if possible to assist with texture cache hits.

## Working with Dissolve Maps

The "Dissolve Map" is a grayscale texture where pixel values represent the order in which pixels disappear. Darker pixels (values closer to 0) vanish first, while lighter pixels (values closer to 1) vanish last.

1. **Format**: Use a single-channel (Luminance) or RGBA texture where the Red channel acts as the noise source.
2. **Filtering**: `Linear` filtering is recommended for the noise map to ensure the transition edge remains smooth rather than pixelated.
3. **Wrapping**: `Repeat` wrap mode is usually required for the noise texture to ensure the pattern covers the entirety of the actor's quad.

## Example Implementation

This example demonstrates creating a basic dissolve transition where an actor gradually "dissolves" away over two seconds.

```cpp
#include <dali/dali.h>
#include <dali-toolkit/dali-toolkit.h>

using namespace Dali;
using namespace Dali::Toolkit;

void SetupDissolve(Actor actor)
{
    // 1. Create the Effect
    Property::Map dissolveMap = CreateDissolveEffect(); 
    
    // 2. Set the Noise Texture (Required for the effect to function)
    Texture noiseTexture = LoadTexture("dissolve_noise.png");
    dissolveMap["uDissolveSampler"] = noiseTexture;
    
    // 3. Apply to Actor
    actor.SetProperty(Actor::Property::SHADER, dissolveMap);
    
    // 4. Animate the progress
    Animation animation = Animation::New(2.0f);
    animation.AnimateTo(Property(actor, "uDissolveProgress"), 1.0f);
    animation.Play();
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/dissolve-effect)
