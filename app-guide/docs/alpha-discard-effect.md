---
id: alpha-discard-effect
title: "Alpha Discard Effect"
sidebar_label: "Alpha Discard Effect"
---
## Understanding [Alpha Discard Effect](./alpha-discard-effect.md)

The `AlphaDiscardEffect` is a specialized shader effect designed to optimize [rendering](./rendering.md) by performing a hard cut-off on fragment transparency. Unlike standard blending, which performs expensive arithmetic to combine pixel colors with the background, this effect discards fragments entirely if their alpha value falls below a defined threshold.

You should use this effect when [rendering](./rendering.md) textures with binary transparency (e.g., aliased [text](./text.md), simple icons, or masks) where smooth interpolation is unnecessary. By discarding fragments early, you reduce fragment processing overhead and mitigate overdraw issues, making it a powerful tool for performance-critical UI [layouts](./layouts.md).

## Applying the Effect to Actors

To apply the `AlphaDiscardEffect`, you instantiate the effect [object](./object.md) and apply it to an existing `Actor` or `Control`. This process modifies the actor's [rendering](./rendering.md) behavior by injecting the discard logic into the fragment shader pipeline.

### Attaching the Effect
The effect is applied by setting it as a property or adding it to the actor's visual hierarchy. Ensure the actor has a valid `ImageVisual` or equivalent source content, as the discard operation is performed on the input pixel data.

```cpp
#include <dali/dali.h>
#include <dali-toolkit/dali-toolkit.h>

using namespace Dali;
using namespace Dali::Toolkit;

// Assuming 'myActor' is an existing Actor in your scene
void ApplyDiscard(Actor myActor)
{
  // Instantiate the effect
  Property::Map effectMap;
  effectMap["shader"] = Property::Map().Add("defines", "ALPHA_DISCARD_ENABLED");
  
  // Apply to the actor's renderer or visual properties
  myActor.SetProperty(Actor::Property::SHADER, effectMap);
}
```

> Note: If you require more complex fragment manipulation, consider the standard Alpha Masking components. → See: [AlphaMaskEffect]

## Configuring Discard Thresholds

The core of the `AlphaDiscardEffect` is the `discardThreshold` property, which determines the cut-off point for fragment visibility. Fragments with an alpha value lower than this threshold are completely discarded, resulting in a transparent pixel.

### Setting the Threshold
The `discardThreshold` is a floating-point value ranging from 0.0 (nothing is discarded) to 1.0 (everything is discarded). Tuning this value is essential for removing "halo" artifacts around aliased textures.

```cpp
void ConfigureThreshold(Actor myActor, float threshold)
{
  // Property index for the alpha discard threshold
  const int DISCARD_THRESHOLD_INDEX = 10; 
  
  // Set the threshold to the desired value
  myActor.SetProperty(DISCARD_THRESHOLD_INDEX, threshold);
}
```

> Warning: Setting the threshold too high may result in "chunky" edges or the loss of intended visual detail. Always test with the specific source texture asset being used.

## Managing Performance and Overdraw

Because the `AlphaDiscardEffect` utilizes the GPU's `discard` keyword (or equivalent functionality), it effectively prevents the blending unit from performing unnecessary color writes. This is particularly useful in complex UI stacks where multiple transparent layers would normally trigger high overdraw costs.

### Optimization Best Practices
* **Minimize State Changes:** Reuse the same effect instance across multiple actors when possible.
* **Avoid Over-Clipping:** Only use this effect when you have hard-edged transparency; using it on gradients will result in visible stepping.
* **Layering:** Place actors using this effect at the bottom of the z-stack to maximize the impact of the discard operation on subsequent layers.

```cpp
// Example: Creating a high-performance icon array
void CreateIconRow(Actor parent)
{
  for(int i = 0; i < 5; ++i)
  {
    ImageView icon = ImageView::New("icon.png");
    icon.SetProperty(Actor::Property::POSITION, Vector3(i * 50.0f, 0.0f, 0.0f));
    
    // Apply discard to ensure icons don't create additive overdraw
    icon.SetProperty(Actor::Property::SHADER, Property::Map().Add("defines", "ALPHA_DISCARD_ENABLED"));
    
    parent.Add(icon);
  }
}
```

## Visual Debugging and Troubleshooting

If your textures appear to have jagged edges or missing segments, it is usually due to an incorrect `discardThreshold` or incompatible alpha channels in the source image. Ensure your textures are pre-multiplied appropriately if the rendering pipeline requires it.

### Debugging Techniques
* **Threshold Sweep:** Implement a temporary slider in your debug UI that modifies the `discardThreshold` in real-time to find the "sweet spot" for your assets.
* **Visual Inspection:** If the edges are too sharp, lower the threshold; if you see "ghosting" or artifacts, raise it.
* **Shader Compatibility:** The `AlphaDiscardEffect` relies on standard fragment shader support. If it fails to render, verify that the underlying `Actor` supports custom shaders and that no other competing effects are overwriting the `SHADER` property.

> Note: Platform-level detail: Custom shader logic involving discard may behave differently on specific GPU architectures regarding early-z testing. Refer to the platform guide for device-specific performance characteristics.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/alpha-discard-effect)
