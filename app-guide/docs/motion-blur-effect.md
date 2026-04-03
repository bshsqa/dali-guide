---
id: motion-blur-effect
title: "Motion Blur Effect"
sidebar_label: "Motion Blur Effect"
---
## Understanding the [Motion Blur Effect](./motion-blur-effect.md)

The `MotionBlurEffect` is a specialized shader component designed to simulate visual persistence by applying directional blurring to an `Actor` based on its perceived movement. Unlike generic blur filters, this effect dynamically calculates the displacement of pixels across frames, creating a sense of speed and fluidity that is essential for high-fidelity animations or rapid UI transitions.

Use the `MotionBlurEffect` when your application requires a professional, "cinematic" feel for moving objects, such as cards sliding through a stack or items scrolling quickly in a list. It is distinct from other effects because it leverages velocity vectors to determine the blur direction and magnitude, ensuring the blur follows the path of motion rather than being a uniform radial or Gaussian smear.

## Applying Motion Blur to Actors

Applying the motion blur effect involves wrapping the target `Actor` within the effect container or applying the shader effect directly to the actor's renderer. This establishes the influence area where the motion calculations will be processed by the GPU.

### Implementing the Effect

To apply the effect, instantiate the `MotionBlurEffect` and associate it with your target actor. The effect processes the transformation matrix of the actor to determine the blur vector.

```cpp
#include <dali-toolkit/dali-toolkit.h>

using namespace Dali;
using namespace Dali::Toolkit;

// Create an actor
Actor myActor = Actor::New();
myActor.SetSize(200.0f, 200.0f);

// Apply the motion blur effect
// (Note: MotionBlurEffect is instantiated via the Toolkit Effect factory)
Effect motionBlur = MotionBlurEffect::New();
myActor.SetShaderEffect(motionBlur);
```

> Note: For the effect to calculate motion correctly, the target actor must have its position or transform updated across subsequent frames; static objects will result in zero blur.

## Configuring Blur Intensity and Quality

Fine-tuning the `MotionBlurEffect` allows you to balance visual impact with rendering performance. You can adjust how "stretched" the blur appears and how many samples are taken to render that stretch.

### Adjusting Blur Parameters

The intensity of the effect is controlled by the strength property, while the sample count determines the smoothness of the blur. Higher sample counts result in cleaner gradients but consume more GPU cycles.

* **Strength**: Defines the magnitude of the velocity vector multiplication. 
* **Sample Count**: Defines the number of texture lookups performed per pixel.

```cpp
// Set high intensity for dramatic effect
motionBlur.SetProperty(MotionBlurEffect::Property::STRENGTH, 2.5f);

// Set sample count to 8 for a balance of quality and performance
motionBlur.SetProperty(MotionBlurEffect::Property::SAMPLE_COUNT, 8);
```

## Managing Performance and Frame Budgeting

Because motion blur is a post-processing operation that iterates over pixels multiple times, it can be resource-intensive on low-end hardware. Effective budgeting involves capping the maximum sample count and restricting the effect to only the most critical UI elements.

### Optimization Strategies

To maintain 60FPS or higher, monitor the `SAMPLE_COUNT`. If you observe dropped frames during fast animations, consider reducing the sample count or limiting the blur to only active, moving objects rather than the entire scene background.

> Warning: High sample counts on large high-resolution textures can significantly impact GPU fill rate. Always test your blur intensity on the lowest target hardware profile.

## Handling Dynamic Motion Blur State

You can animate the blur properties or toggle the effect's influence in real-time. This is particularly useful for fading the blur in as an object begins to accelerate.

### Animating Properties

Dali properties can be animated using the `Animation` class. Animating the `STRENGTH` property during an object's entry animation provides a polished transition into the moving state.

```cpp
Animation animation = Animation::New(0.5f);
animation.AnimateTo(Property(motionBlur, MotionBlurEffect::Property::STRENGTH), 0.0f);
animation.Play();
```

## Common Implementation Patterns

The most effective use of `MotionBlurEffect` occurs during high-speed transitions. By tying the blur strength to the velocity of an animation, the effect naturally intensifies as the object moves faster and vanishes when the object comes to a rest.

### Swipeable Cards
When implementing a deck of swipeable cards, apply the `MotionBlurEffect` only to the card currently being interacted with. Disable the effect once the card settles into its final position to save rendering resources.

→ See: [ShaderEffects](parent-page-link)

```cpp
// Pattern: Enable effect on drag, disable on release
void OnCardDrag(Actor actor, float velocity) {
    float strength = std::min(velocity * 0.01f, 5.0f);
    motionBlur.SetProperty(MotionBlurEffect::Property::STRENGTH, strength);
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/motion-blur-effect)
