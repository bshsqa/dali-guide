---
id: render-effects
title: "render-effects"
sidebar_label: "render-effects"
---
## Introduction to Render Effects

The DALi `render-effects` framework provides a high-level API for applying complex visual post-processing and graphical transformations to UI components. By abstracting the underlying shader logic and framebuffer operations, these effects allow developers to create sophisticated [visuals](./visuals.md) like blurs and masking with minimal boilerplate.

Render Effects are distinct from standard property animations as they modify the [rendering](./rendering.md) pipeline output directly. Use these effects when you need to perform dynamic texture processing, such as creating depth-of-field backgrounds or applying non-rectangular cutouts to UI views, without manually managing custom shaders or framebuffers.

## Render Effect Lifecycle and Threading

The `render-effects` components operate within the DALi [threading](./threading.md) model, maintaining a strict separation between the Application (Main) thread and the Render thread. When an effect is initialized on the main thread, the engine prepares the necessary GPU resources and command buffers for the upcoming frame.

### Lifecycle Management
Effects follow the standard handle-based lifecycle [common](./common.md) in DALi. When an effect is instantiated via a `New()` call, it is associated with an underlying core [object](./object.md). 

> Note: Because these effects involve off-screen [rendering](./rendering.md) (fbo usage), they have a higher memory overhead than standard visual properties. Ensure that effects are properly cleared or destroyed when a View is removed from the stage to allow the engine to reclaim GPU memory.

## Integration with the DALi Rendering Pipeline

Render Effects hook into the DALi frame [rendering](./rendering.md) graph by intercepting the render call of an `Actor` or `View`. The framework performs a sequence of passes, typically capturing the target component into an intermediate texture, applying the kernel (e.g., Gaussian blur or alpha mask), and compositing it back into the scene.

### Implementing Custom Behaviors
While the provided classes cover [common](./common.md) scenarios, the integration API ensures that these effects remain performant by leveraging the `SetBlurOnce` pattern. This allows the engine to optimize by [rendering](./rendering.md) the effect only when the scene state changes, rather than continuously on every frame refresh.

## Sub-Components Overview

The `render-effects` family provides three specialized implementations:

- **Background Blur Effect**: Blurs the content behind a specific UI component. → See: [[BackgroundBlurEffect](./background-blur-effect.md)]
- **Gaussian Blur Effect**: Applies a general-purpose Gaussian blur to the owner view and its children. → See: [[GaussianBlurEffect](./gaussian-blur-effect.md)]
- **Mask Effect**: Uses an alpha mask to clip or shape the owner view based on a provided texture. → See: [[MaskEffect](./mask-effect.md)]

## Best Practices and Performance Considerations

Performance is critical when using post-processing effects, as they frequently require multiple render passes and additional texture memory.

- **Use `SetBlurOnce(true)`**: If your UI does not require real-time updates (e.g., a static overlay blur), always set this to `true`. This instructs the engine to cache the result, drastically reducing GPU load.
- **Adjust Downscale Factors**: For large areas, use `SetBlurDownscaleFactor` to reduce the resolution of the blur buffer. A factor of 0.5 or lower can provide a significant performance boost with minimal visual degradation.
- **Resource Management**: Avoid creating and destroying effects inside an [animation](./animation.md) loop. Configure properties like `BlurRadius` or `BlurOpacity` using the provided `AddBlurStrengthAnimation` and `AddBlurOpacityAnimation` methods to ensure hardware-accelerated transitions.

### Example: Implementing a Background Blur

The following example demonstrates how to create a background blur effect and animate its strength, which is a [common](./common.md) pattern for modal dialogs or overlay UI elements.

```cpp
#include <dali/dali.h>
#include <dali/devel-api/ui/render-effects.h>

using namespace Dali;
using namespace Dali::Ui;

void SetupBlur(View myView, Animation& blurAnim)
{
  // 1. Create the effect
  BackgroundBlurEffect blurEffect = BackgroundBlurEffect::New(20u);
  
  // 2. Configure downscale to save performance
  blurEffect.SetBlurDownscaleFactor(0.25f);
  
  // 3. Add to view (assuming internal API handles association)
  // myView.AddEffect(blurEffect);

  // 4. Animate the blur strength from 0 to 20
  blurEffect.AddBlurStrengthAnimation(
    blurAnim, 
    AlphaFunction::LINEAR, 
    TimePeriod(0.0f, 1.0f), 
    0.0f, 
    20.0f
  );
  
  blurAnim.Play();
}
```

> Warning: When using `SetSourceActor` or `SetStopperActor` in `[BackgroundBlurEffect](./background-blur-effect.md)`, ensure the handles remain valid throughout the effect's lifetime. If a source actor is destroyed, the effect may fail to render the background content correctly.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/render-effects)
