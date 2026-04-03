---
id: animation
title: "animation"
sidebar_label: "animation"
---
## Introduction to DALi Animation

The DALi [animation](./animation.md) framework provides a powerful, high-performance system for creating fluid visual transitions and dynamic property changes across your application's scene graph. By offloading property interpolation to the render thread, it ensures smooth, jank-free motion even when the application logic is busy.

You should use the [animation](./animation.md) framework whenever you need to transition an `Actor` between states, such as sliding menus, fading UI elements, or rotating icons. It is distinct because it is tightly integrated with the DALi property system, allowing you to animate any animatable property of an `Actor` with precise control over timing, curves, and physical behavior.

## Basic Animation Control

Animation control allows you to instantiate the `Animation` class, define the sequence of property changes, and manage the playback lifecycle. This is the foundation for creating any time-based visual effect in your application.

### Managing Animation Playback
The `Animation` [object](./object.md) acts as a container for your transition sequences. You create an [animation](./animation.md), define the changes for specific actors, and then trigger the playback to see the results on screen.

> Note: Animations operate on `Actor` properties. Ensure your actors are already on the stage when starting animations to avoid undefined behavior.

```cpp
// Example: Creating and playing a simple animation
Dali::Animation animation = Dali::Animation::New(2.0f); // 2-second duration
animation.AnimateTo(Property(actor, Actor::Property::POSITION), Vector3(100.0f, 100.0f, 0.0f));
animation.Play();
```

## Defining Animation Curves and Timing

To achieve natural-looking motion, animations rarely move at a constant speed. The framework provides `AlphaFunctions` to define acceleration profiles and `TimePeriods` to control when in the animation timeline specific changes occur.

### Using AlphaFunctions
An `AlphaFunction` maps a linear time progression (0.0 to 1.0) to a non-linear value, creating effects like ease-in, ease-out, or bouncing transitions.

```cpp
// Example: Using an easing function for a smooth transition
Dali::Animation animation = Dali::Animation::New(1.0f);
animation.SetDefaultAlphaFunction(Dali::AlphaFunction::EASE_IN_OUT);
animation.AnimateTo(Property(actor, Actor::Property::SCALE), Vector3(2.0f, 2.0f, 2.0f));
animation.Play();
```

## Complex Motion with KeyFrames and Paths

For non-linear movement that isn't easily captured by a simple start-to-end interpolation, KeyFrames and Paths allow for precise control over an actor's trajectory and property state at specific points in time.

### Implementing KeyFrame Sequences
A `KeyFrame` object allows you to define a series of values for an actor's property, which the animation system will interpolate between as the animation progresses.

> Note: KeyFrame sequences are best used for complex animations where an actor must follow a specific path or change behavior at multiple intervals.

## Dynamic Property Constraints

Constraints provide a mechanism for creating reactive UI elements by mathematically linking one actor's property to another. Unlike standard animations, constraints are evaluated every frame during the update process.

### Linking Properties
You can define a constraint where a child actor's position is always relative to its parent, or where an actor's opacity is linked to its current scale, ensuring the UI remains consistent regardless of manual property changes.

→ See: [Dali::Actor]

## Advanced Constrainers

Advanced constrainers, such as `LinearConstrainer` and `PathConstrainer`, automate complex property updates. These are primarily used for physical behaviors like orbital movement or maintaining specific relationships between multiple objects in a 3D space.

> Warning: Excessive use of complex constraints can impact performance. Only use them when the reactive behavior cannot be achieved via standard animations.

## Spring-Based Animations

Spring-based animations bring a physical, high-fidelity feel to your user interface. By configuring `SpringData`, you can simulate mass, damping, and stiffness, allowing UI elements to "settle" into place with a realistic overshoot or bounce.

### Configuring SpringData
Spring animations are ideal for touch-responsive UI components, such as scroll views or swipable cards, where the motion feels more natural if it reacts to the user's velocity and interaction state.

```cpp
// Example: Setting up a spring animation
Dali::SpringData spring(200.0f, 10.0f); // Stiffness and Damping
animation.AnimateTo(Property(actor, Actor::Property::POSITION), Vector3(500.0f, 0.0f, 0.0f), spring);
animation.Play();
```

*For details regarding platform-specific optimizations or internal [rendering](./rendering.md) pipeline adjustments, please refer to the platform-level developer guide.*

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animation)
