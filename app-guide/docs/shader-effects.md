---
id: shader-effects
title: "shader-effects"
sidebar_label: "shader-effects"
---
## Introduction to [Shader](./shader.md) Effects

The DALi `shader-effects` module provides a library of pre-configured, high-performance visual processing shaders designed for rapid application UI enhancement. By abstracting complex GPU fragment shader logic into intuitive C++ objects, these effects allow developers to apply sophisticated visual transformations—such as blurring, dissolving, or font [rendering](./rendering.md)—without writing raw GLSL code.

Use these effects when you need to extend the standard appearance of `Actor` or `ImageView` components with dynamic, GPU-accelerated visual polish. They are distinct from custom shader implementations because they are optimized for [common](./common.md) UI design patterns, ensuring consistency and performance across different hardware configurations within the DALi ecosystem.

## Applying Effects to Actors

[Shader](./shader.md) effects are applied to actors to modify their visual output during the [rendering](./rendering.md) pass. By attaching these effects, you intercept the standard fragment processing pipeline to inject custom logic that manipulates colors, transparency, or pixel coordinates.

### Attaching Effects

To apply an effect, you typically instantiate the effect [object](./object.md) and apply it to an existing actor. The effect processes the underlying geometry and textures of the actor to render the requested visual modification.

> Note: While many effects function as standard DALi objects, some complex effects may require specific input textures or parameter updates during the application lifecycle to function as intended.

```cpp
// Example: Applying a general shader effect structure to an actor
auto myActor = ImageView::New( "my_image.png" );
// ... instantiate the specific effect ...
auto effect = MyEffect::New(); 
// Apply the effect to the actor's visual property
myActor.SetProperty( Toolkit::Visual::Property::EFFECT, effect.GetPropertyMap() );
Stage::GetCurrent().Add( myActor );
```

## Shader Effects Sub-Components

This section summarizes the primary visual effects available. Each provides unique capabilities for animating and styling your UI components.

### Alpha Discard Effect
The Alpha Discard effect allows for the efficient removal of pixels based on an alpha threshold, which is useful for creating complex transparency masks or cut-out animations.
→ See: [Alpha Discard Effect](alpha-discard-effect.md)

### Dissolve Effect
The Dissolve effect enables smooth transition animations where an object appears to fade away or reveal itself through noise patterns or arbitrary thresholding.
→ See: [Dissolve Effect](dissolve-effect.md)

### Distance Field Effect
This effect is primarily utilized for rendering high-quality, resolution-independent vector graphics or font glyphs, ensuring smooth edges even at high zoom levels.
→ See: [Distance Field Effect](distance-field-effect.md)

### Image Region Effect
The Image Region effect allows you to dynamically crop or define specific UV sub-regions of an image texture, which is ideal for sprite sheet animation or UI component scaling.
→ See: [Image Region Effect](image-region-effect.md)

### Motion Blur Effect
The Motion Blur effect simulates the visual blur occurring when an object moves rapidly across the screen, enhancing the perceived fluidity of animations.
→ See: [Motion Blur Effect](motion-blur-effect.md)

## Performance and Resource Management

Shader effects run on the GPU, and their complexity directly impacts frame rendering time. To ensure smooth performance, it is critical to balance the number of simultaneous effects applied to a single actor.

> Warning: Excessive use of fragment-heavy shaders, particularly on complex geometry, can lead to frame drops. Always test effects on the target hardware to ensure the application maintains a consistent 60 FPS.

- **Reuse Effects:** Where possible, share effect configurations between multiple actors to reduce memory overhead.
- **Minimize Updates:** Avoid changing effect properties every frame; update only when the visual state changes to prevent unnecessary GPU state re-validation.
- **Texture Sizes:** When using effects that require input textures (like masks), keep texture resolutions as small as possible to minimize memory bandwidth usage.

## Common Property Configuration

Most shader effects in DALi utilize a shared property map system. This consistency allows developers to create helper functions to animate or transition between effect states using standard animation APIs.

### Configuring Parameters

You can interact with effect properties using the `SetProperty` and `GetProperty` methods available on the UI components hosting the effects. By using a `Property::Map`, you can set multiple uniform values simultaneously, allowing the shader to adjust behavior (like intensity or threshold) dynamically.

```cpp
// Define properties for an effect
Property::Map effectMap;
effectMap[ "uThreshold" ] = 0.5f;
effectMap[ "uSmoothness" ] = 0.1f;

// Apply to an actor
myActor.SetProperty( Toolkit::Visual::Property::EFFECT, effectMap );

// Animate a property of the effect
Animation animation = Animation::New( 1.0f );
animation.AnimateTo( Property( myActor, Toolkit::Visual::Property::EFFECT ), 1.0f, Property::Map().Add( "uThreshold", 1.0f ) );
animation.Play();
```

> Note: Certain platform-level details regarding custom shader injection via the `devel-api` are excluded here; please consult the Platform Integration Guide if your requirements exceed these standard built-in effects.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/shader-effects)
