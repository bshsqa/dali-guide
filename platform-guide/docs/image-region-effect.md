---
id: image-region-effect
title: "Image Region Effect"
sidebar_label: "Image Region Effect"
---
## Introduction to [Image Region Effect](./image-region-effect.md)

The `image-region-effect` module provides a specialized mechanism for isolating and [rendering](./rendering.md) a sub-rectangle of a larger texture without requiring the texture source itself to be cropped. It is the ideal choice when your application needs to display specific sprites from a sprite sheet or focus on a region of interest within a high-resolution buffer while minimizing the overhead of CPU-side image processing.

Unlike generic shader effects that manipulate color or spatial distortion, the `image-region-effect` is distinct because it operates directly on the texture coordinate space (UV mapping). By mathematically constraining the sampling range within the fragment shader, it allows developers to treat a portion of an existing `Dali::Toolkit::ImageView` or `Dali::Actor` as a standalone image, significantly reducing memory footprint by leveraging shared atlas textures.

## [Shader](./shader.md)-Based Implementation Details

This section explains how the module maps spatial clipping to GPU operations to ensure minimal sampling latency. The effect processes the input `Rect` and transforms it into uniform data passed directly to the fragment shader.

### Coordinate Transformation
The effect intercepts the default texture coordinate attribute and applies a normalization transform based on the `uRegion` uniform. This uniform defines the `[x, y, width, height]` in normalized range `[0.0, 1.0]`. By defining this in the fragment shader, DALi avoids recompiling the geometry for every frame, allowing for smooth [animation](./animation.md) of the visible region (e.g., sprite sheet cycling) by simply updating the uniform values.

```cpp
// Example: Manually updating the region via Property system
Dali::Toolkit::ImageView imageView = Dali::Toolkit::ImageView::New(resourcePath);
imageView.SetProperty(Dali::Toolkit::ImageView::Property::IMAGE, imageSource);

// Define a region: [x, y, width, height] normalized to texture dimensions
Vector4 region(0.1f, 0.1f, 0.5f, 0.5f);
imageView.SetProperty(Toolkit::ImageRegionEffect::Property::REGION, region);
```

## API Reference and Property Mapping

This section outlines the core properties used to control the region-clipping behavior. All properties are accessed via the standard `SetProperty` and `GetProperty` interface of the DALi property system.

### Properties
*   **`REGION`**: A `Vector4` property representing the normalized area to be rendered.
    *   `x`: Horizontal start position (0.0 to 1.0).
    *   `y`: Vertical start position (0.0 to 1.0).
    *   `width`: Horizontal extent of the region.
    *   `height`: Vertical extent of the region.

> Note: If the defined region exceeds the bounds of the texture (e.g., `x + width > 1.0`), the behavior defaults to the texture wrapping mode defined in the sampler.

```cpp
#include <dali/dali.h>
#include <dali-toolkit/dali-toolkit.h>

void SetupRegionEffect(Dali::Actor actor) {
  // Apply a 50% central zoom on the texture
  Vector4 crop(0.25f, 0.25f, 0.5f, 0.5f);
  actor.SetProperty(Toolkit::ImageRegionEffect::Property::REGION, crop);
}
```

## Lifecycle and Resource Management

Proper management of the `image-region-effect` is crucial for maintaining a clean render state and preventing GPU memory leaks. 

### Initialization and Cleanup
Because the effect is often applied to an `Actor` or `[ImageView](./static-image-view.md)`, it follows the lifecycle of its host object. When the host actor is removed from the stage, the shader resources are released by the DALi internal resource manager. To ensure optimal performance, avoid creating new effect instances per-frame; instead, reuse the actor and modify the `REGION` property dynamically.

```cpp
// Correct way to update animation without recreating resources
void AnimateRegion(Dali::Actor actor) {
  Animation anim = Animation::New(2.0f);
  anim.AnimateTo(Property(actor, Toolkit::ImageRegionEffect::Property::REGION), Vector4(0.0f, 0.0f, 1.0f, 1.0f));
  anim.Play();
}
```

## Integration with Texture Atlases

The `image-region-effect` is specifically architected to work with texture atlases, which are multiple images packed into a single texture memory buffer. By utilizing this module, you can render an atlas segment without creating temporary textures for each sub-image.

### Batch Rendering Efficiency
By grouping actors that reference the same underlying atlas texture—even if they display different `image-region-effect` segments—you enable the DALi renderer to perform batching. This reduces draw calls significantly, as the GPU does not need to switch texture bindings between renders.

→ See: [TextureAtlasManager]

## Performance Considerations and Thread Safety

The `image-region-effect` is highly performant because it shifts the cropping logic to the fragment shader, which is executed in parallel across the GPU cores. However, developers must be mindful of thread boundaries when updating properties.

### Thread Safety
*   **Main Thread**: All calls to `SetProperty` must occur on the main thread where the DALi `Application` or `Core` instance resides.
*   **Update Thread**: The DALi update thread consumes the values set by the main thread. While `SetProperty` is thread-safe in its interface, rapid-fire updates (e.g., from a high-frequency sensor) should be throttled to the frame rate to prevent the update thread from being overwhelmed with uniform updates.

> Warning: Avoid modifying the `REGION` property inside a `Signal` callback that triggers during the render pass, as this may lead to undefined visual behavior or flickering. Always schedule updates to occur at the start of the frame.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/image-region-effect)
