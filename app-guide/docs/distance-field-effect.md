---
id: distance-field-effect
title: "Distance Field Effect"
sidebar_label: "Distance Field Effect"
---
## Understanding Distance Field Effects

Distance Field (DF) [rendering](./rendering.md) is a technique used in the DALi framework to achieve resolution-independent scaling for glyphs and vector shapes. Unlike traditional bitmap [rendering](./rendering.md), which loses quality when scaled or rotated, distance fields store the distance to the nearest boundary of the shape within each texel.

By utilizing custom shader programs within the DALi [rendering](./rendering.md) pipeline, the system can determine whether a pixel is inside or outside the shape boundary by comparing the sampled distance value against a predefined threshold. This allows for high-quality, crisp edges regardless of the transformation applied to the `Visual` or `Actor`.

### Core Concept
The distance field approach transforms the fragment shader calculation from a simple texture lookup into a mathematical evaluation of a function. This effectively turns standard texture data into scalable geometry, enabling features like smooth anti-aliasing, soft shadows, and dynamic outlines without the memory overhead associated with high-resolution, multi-sized texture sets.

## Applying Effects to Actors

In DALi, effects are applied by creating a `Shader` [object](./object.md) and attaching it to a `Renderer`. The `Renderer` is then associated with an `Actor` via a `Visual`. To implement distance field [rendering](./rendering.md), you must load the appropriate GLSL source code, which contains the logic for sampling the distance field texture and performing the thresholding calculations.

### Implementation Workflow
1. Create a `Shader` [object](./object.md) specifying the vertex and fragment source.
2. Create a `Renderer` using geometry and material data.
3. Apply the `Shader` to the `Renderer`.
4. Register the `Renderer` with an `Actor` using the `AddRenderer` method.

```cpp
// Example: Attaching a custom distance field shader
std::string vertexShader = "...";
std::string fragmentShader = "...";

Dali::Shader shader = Dali::Shader::New(vertexShader, fragmentShader);
Dali::Renderer renderer = Dali::Renderer::New(geometry, textureSet);
renderer.SetShader(shader);

// Attach the renderer to the target actor
actor.AddRenderer(renderer);
```

## Configuring Visual Properties

Distance field effects provide granular control over the visual output through `Uniforms` mapped to the shader. These properties dictate how the distance value is interpreted by the fragment shader.

### Common Uniforms
*   **Smoothing:** A value representing the transition width at the edge of the shape. Increasing this value produces blurrier edges, while setting it near zero creates a hard, pixel-perfect edge.
*   **Glow/Outline:** By adding an offset to the distance threshold, developers can inflate or deflate the shape to create outline effects.
*   **Shadow Offset:** Applying a secondary thresholding pass with a coordinate transformation can simulate drop shadows.

```cpp
// Updating shader uniform values to adjust edge smoothing
const float smoothingValue = 0.05f;
renderer.RegisterProperty("uSmoothing", smoothingValue);
```

## Optimizing Performance

Distance field effects involve mathematical operations in the fragment shader that are more expensive than standard texture sampling. To maintain high frame rates, especially on mobile hardware, developers should follow these optimization strategies:

*   **Precision Qualifiers:** Use `lowp` or `mediump` for distance calculations in the GLSL fragment shader where high precision is not strictly required.
*   **Texture Atlas Usage:** Combine multiple distance field shapes into a single texture atlas to reduce draw calls and state changes.
*   **Conditional Logic:** Avoid heavy branching within the fragment shader. The thresholding operation is typically a simple `smoothstep` function, which is hardware-accelerated.

```cpp
// Using a hint to ensure efficient execution
renderer.SetProperty(Dali::Renderer::Property::DEPTH_INDEX, 10);
```

## Handling Dynamic Content

When text or vector shapes change, the underlying distance field data must be refreshed. If the distance field is generated dynamically, ensure that the texture data is uploaded to the GPU using the appropriate `PixelData` update methods.

For animated UI elements, avoid regenerating the distance field texture every frame. Instead, modify the `[Shader](./shader.md)` uniforms (like thresholds or offsets) to animate the visual appearance, as updating properties is significantly more efficient than recreating texture resources.

```cpp
// Updating properties during an animation callback
void OnAnimate(float progress)
{
  float dynamicThreshold = 0.5f + (progress * 0.1f);
  renderer.RegisterProperty("uThreshold", dynamicThreshold);
}
```

## Common Use Cases and Examples

### Scalable Typography
Distance fields are the standard for rendering high-quality fonts. By storing the font as a distance field, labels and text inputs maintain perfect crispness at any zoom level.

### High-Resolution UI Icons
Icons can be stored as small distance field textures and scaled up for high-DPI displays without storage penalties.

```cpp
// Example: Creating a soft-glow icon effect
void SetupGlowIcon(Dali::Renderer renderer)
{
    // Set the base distance threshold
    renderer.RegisterProperty("uThreshold", 0.5f);
    // Add an additive glow effect
    renderer.RegisterProperty("uGlowWidth", 0.2f);
    renderer.RegisterProperty("uGlowColor", Vector4(1.0f, 1.0f, 0.0f, 1.0f));
}
```

These techniques provide a robust foundation for building modern, responsive, and visually sophisticated UIs within the DALi ecosystem.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/distance-field-effect)
