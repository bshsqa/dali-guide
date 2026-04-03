---
id: motion-blur-effect
title: "Motion Blur Effect"
sidebar_label: "Motion Blur Effect"
---
## Introduction to [Motion Blur Effect](./motion-blur-effect.md)

The `motion-blur-effect` is a specialized post-processing shader designed to simulate temporal visual persistence, commonly known as motion blur, for elements undergoing rapid transformation or translation. Unlike standard static blur effects, this module utilizes directional vectors to compute a directional smear based on the velocity of the UI element, providing a sense of speed and fluidity to animations that would otherwise appear jittery at lower frame rates.

Developers should employ the `motion-blur-effect` when creating high-fidelity transitions, fast-scrolling lists, or game-like UI elements where visual continuity is paramount during fast movement. Its primary distinction from sibling components, such as `blur-effect` or `gaussian-blur-effect`, lies in its dependency on directional velocity vectors rather than uniform spatial convolution, allowing for context-aware blurring based on the movement trajectory.

→ See: [BlurEffect]

## Effect Parameters and Uniforms

The `motion-blur-effect` exposes a set of public properties that allow for fine-tuned control over the visual trail intensity, sampling density, and decay rate. These properties map directly to shader uniforms that govern the fragment processing stage.

### Controlling Blur Magnitude
The effect intensity is primarily controlled via a normalized vector property, which dictates the direction and the "strength" of the smear.

*   **WHAT:** Defines the directional vector of the blur and the amount of influence the current velocity has on the frame.
*   **WHY:** Used to calibrate the blur to match the physical perceived speed of an actor’s [animation](./animation.md).
*   **HOW:** The property accepts a `Vector2` (x, y) where the length represents the blur radius and the orientation represents the directional smear.
*   **CODE:**
```cpp
// Applying motion blur to a specific Actor
Dali::Toolkit::ShaderEffect motionBlur = Dali::Toolkit::MotionBlurEffect::New();
Dali::Vector2 blurDirection(10.0f, 0.0f); // Horizontal smear
motionBlur.SetProperty(Dali::Toolkit::MotionBlurEffect::Property::BLUR_DIRECTION, blurDirection);
myActor.SetProperty(Dali::Actor::Property::SHADER_EFFECT, motionBlur);
```

> Note: Setting `BLUR_DIRECTION` to zero effectively disables the blur, reverting the fragment shader to a pass-through state to save GPU cycles.

## Integration with DALi Actors

Integrating `motion-blur-effect` requires attaching the effect instance to the `SHADER_EFFECT` property of any `Dali::Actor` or `Dali::Toolkit::View`. 

### Attachment and Lifecycle
The effect instance manages its own lifecycle via a handle-based reference counting system; however, it remains dependent on the host actor's render-tree attachment.

*   **WHAT:** Associates the blur effect with the render geometry of an actor.
*   **WHY:** Ensures the fragment shader is invoked during the actor's draw call, allowing access to the frame buffer's previous state.
*   **HOW:** Invoke `New()` to instantiate the effect, and assign it using `SetProperty`.
*   **CODE:**
```cpp
void CreateBlurredElement() {
    auto actor = Dali::Actor::New();
    auto effect = Dali::Toolkit::MotionBlurEffect::New();
    
    // Set initial configuration
    effect.SetProperty(Dali::Toolkit::MotionBlurEffect::Property::BLUR_STRENGTH, 0.5f);
    
    actor.SetProperty(Dali::Actor::Property::SHADER_EFFECT, effect);
    Dali::Stage::GetCurrent().Add(actor);
}
```

## Internal Rendering Pipeline

The `motion-blur-effect` operates by sampling the color buffer from previous frames and interpolating them with the current fragment color. This process requires the pipeline to maintain a history of rendered fragments to compute the temporal trail.

The engine handles this by utilizing a frame-buffer accumulation pass. During the Render Task phase, the effect reads the current frame buffer and blends it with a cached texture representing the immediate past. This ensures that the "tail" of the motion blur reflects the movement trajectory calculated in the application's animation update. 

> Warning: Because this effect relies on multi-pass rendering or feedback loops, it increases the memory footprint of the render target. Avoid applying this to every actor in a large scene to prevent memory exhaustion on lower-end devices.

## Performance Considerations

High-fidelity motion blur is computationally expensive due to the increased texture sampling required to calculate the trail. To minimize GPU overhead, consider the following optimization strategies:

1.  **Conditional Enabling:** Only attach the effect to actors while they are actively animating. Remove the effect (set to NULL) once the animation completes to free up GPU resources.
2.  **Sampling Density:** Where possible, reduce the number of internal shader samples if the visual fidelity requirements allow, preventing unnecessary fragment processing.
3.  **Layer Scoping:** Apply the effect to the smallest possible sub-tree. Applying the effect to a parent container causes the blur to affect all children, significantly increasing the cost of the accumulation pass.

## Threading and Synchronization

DALi follows an asynchronous thread architecture where the Application side (Main Thread) performs logic and the Render side (Render Thread) executes the drawing.

*   **Property Updates:** All updates to `MotionBlurEffect` properties (e.g., `BLUR_DIRECTION`) are thread-safe because they are queued through the `Property` system. When you call `SetProperty`, the value is marshaled to the render thread automatically.
*   **Synchronization:** Because the render thread processes frames independently of the main thread, the visual blur will always be synchronized with the frame timing, even if the application thread experiences occasional frame drops. Developers should not attempt to manually sync frames; rely on the built-in `Dali::Animation` system to ensure the motion blur correctly reflects the actual movement speed of the actor.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/motion-blur-effect)
