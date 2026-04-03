---
id: distance-field-effect
title: "Distance Field Effect"
sidebar_label: "Distance Field Effect"
---
## Introduction to Distance Field Effects

The `distance-field-effect` is a specialized shader module designed for high-fidelity [rendering](./rendering.md) of vector-based assets, such as [text](./text.md) glyphs or UI icons, using Signed Distance Field (SDF) data. Unlike traditional raster-based image scaling, which suffers from pixelation at higher zoom levels, this effect utilizes a multi-channel or single-channel distance field texture to reconstruct sharp edges at any resolution.

You should use the `distance-field-effect` when you need to render geometric shapes or fonts that must maintain crisp silhouettes while scaling dynamically or when performing complex visual transformations like glowing borders, drop shadows, or embossed finishes. It is distinct from standard texture mapping because the fragment shader calculates the distance to the nearest shape edge, allowing for sophisticated anti-aliasing and procedural styling without needing high-resolution source bitmaps.

## [Shader](./shader.md) Uniforms and Property Mapping

This effect leverages DALi's property system to expose internal shader constants, allowing you to manipulate the visual output dynamically at runtime. These properties map directly to the fragment shader uniforms that compute the distance thresholding, smoothing, and border [rendering](./rendering.md).

### Key Property Mapping
The primary properties used to modulate the distance field are `smoothing`, `outlineColor`, and `outlineWidth`. 
- **Smoothing (float):** Controls the anti-aliasing gradient; lower values create a sharper edge, while higher values create softer, blurred edges.
- **OutlineWidth (float):** Defines the thickness of the stroke around the shape, measured in the distance field's normalized coordinate space.
- **OutlineColor (Vector4):** Sets the RGBA color of the border applied to the shape.

> Note: Changing these properties triggers a uniform [update](./update.md) in the [rendering](./rendering.md) command buffer, which is efficient but should be throttled if updated every frame to prevent CPU-GPU sync stalls.

## Integration Pipeline and Lifecycle

The `distance-field-effect` integrates into the DALi render graph by wrapping a `Shader` [object](./object.md) that is applied to an `Actor` via a `Renderer`. The lifecycle of the effect is intrinsically linked to the lifetime of the `TextureSet` containing the distance field data.

When the actor is added to the stage, the DALi render thread validates that the associated distance field texture is resident in GPU memory. If the texture is updated (e.g., in a dynamic font caching scenario), the developer must ensure that the `TextureSet` is updated before the next render pass to prevent visual artifacts or "tearing" during the interpolation phase of the SDF [rendering](./rendering.md).

## Thread Safety and Resource Constraints

Operations involving the modification of distance field shaders must be performed on the main application thread, as DALi’s scene graph management is not thread-safe for direct property modification. However, the generation of the distance field texture data itself may be offloaded to a worker thread if done via a `PixelBuffer` before being uploaded to a `Texture` [object](./object.md).

> Warning: Avoid modifying the `Shader` [object](./object.md) properties from a background thread. Doing so will result in undefined behavior and potential corruption of the render state during the frame-begin phase.

## Performance Optimization Strategies

Rendering complex distance fields can become fragment-heavy, particularly when using multiple layers of drop shadows or high-complexity outline shaders. To maintain 60/120 FPS, prioritize packing multiple small SDF icons into a single texture atlas rather than using individual textures, which reduces state changes during batching.

When possible, use the `uSmoothing` property to perform soft-shadowing rather than relying on multiple render passes, as this keeps the complexity confined to a single fragment shader execution pass.

## Implementation Guide

To implement the `distance-field-effect`, you instantiate a custom `Shader` or use the pre-configured `Effect` factory, then bind it to a `Renderer` attached to your target `Actor`.

### Basic Implementation Example
```cpp
#include <dali/dali.h>
#include <dali/devel-api/rendering/renderer.h>

using namespace Dali;

// Create an actor with the Distance Field effect
void CreateDistanceFieldActor(Actor& parent, TextureSet& dfTexture)
{
  // 1. Define the shader source (Internal DALi standard distance field shader)
  const std::string vertexShader = "/* ... Standard VS ... */";
  const std::string fragmentShader = "/* ... SDF Fragment Shader ... */";

  Shader shader = Shader::New(vertexShader, fragmentShader);

  // 2. Create the renderer
  Renderer renderer = Renderer::New(Geometry::New(), shader);
  renderer.SetTextures(dfTexture);

  // 3. Configure effect properties
  renderer.RegisterProperty("uSmoothing", 0.05f);
  renderer.RegisterProperty("uOutlineWidth", 0.1f);
  renderer.RegisterProperty("uOutlineColor", Vector4(1.0f, 0.0f, 0.0f, 1.0f));

  // 4. Attach to actor
  Actor actor = Actor::New();
  actor.AddRenderer(renderer);
  parent.Add(actor);
}
```

This setup ensures the `Actor` renders the distance field texture with the specified edge smoothing and outline parameters, maintaining visual integrity regardless of the actor's scale. 

→ See: [ShaderEffects] for general shader lifecycle management.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/distance-field-effect)
