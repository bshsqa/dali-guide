---
id: animation
title: "animation"
sidebar_label: "animation"
---
## Introduction to the DALi Animation Subsystem

The DALi Animation subsystem provides a declarative framework for interpolating property values over time, enabling smooth transitions and complex motion sequences within the scene graph. It serves as the primary engine for visual fluidity, offloading computation from the application thread to the render thread to ensure consistent high-frame-rate performance.

By decoupling the definition of movement from the application logic, the [animation](./animation.md) system allows developers to describe the "what" and "how" of a transformation while the engine manages the "when" during the render loop. This architecture is essential for minimizing UI jank and maintaining responsiveness in high-density graphical applications.

## Core Animation Engine

The `Dali::Animation` class is the central handle for managing sequences of property changes. It allows you to specify start and end states, durations, and timing behaviors for any animatable property on an `Actor`.

### Managing Animation State

An [animation](./animation.md) acts as a container for keyframe-based or duration-based property changes. It is managed via handle-based [object](./object.md) lifetimes, where the [animation](./animation.md) persists as long as a reference is held or it is currently active on the scene.

> Note: Animations are processed on the render thread. Ensure all properties targeted for [animation](./animation.md) are registered with the engine's property system.

```cpp
// Example: Creating a basic animation to fade an actor
Dali::Animation animation = Dali::Animation::New(2.0f); // 2-second duration
animation.AnimateTo(Property(actor, Actor::Property::OPACITY), 0.0f);
animation.Play();
```

## KeyFrames and Alpha Functions

KeyFrames allow for non-linear, multi-point animations where a property value changes at specific timestamps. Alpha functions define the rate of change between these points, providing the "feel" of the motion.

### Defining Motion Profiles

`Dali::KeyFrames` provides a container to define discrete values at normalized time intervals. Once populated, these are applied to properties to create complex, staged movements that go beyond simple linear transitions.

> Warning: Excessive keyframe points can increase memory overhead. Use sparse keyframes for performance-critical UI elements.

## Constraint System Architecture

Constraints define functional dependencies between properties, allowing an `Actor`'s state to be driven automatically by the state of other objects or time. Unlike standard animations, constraints are evaluated every frame by the engine, providing a reactive and persistent linkage.

### Establishing Functional Dependencies

A `Dali::Constraint` utilizes a source property to drive a target property through a developer-defined function. This is the mechanism used for auto-layout, parallax effects, and complex interactions that respond to input or lifecycle events.

→ See: [Dali::Actor]

## Advanced Constrainer Patterns

Specialized constrainers provide pre-baked logic for common motion requirements, such as moving along paths or limiting movement within specific boundaries. These objects abstract the math required for complex geometric tracking.

### Path-Based Motion

`Dali::PathConstrainer` is used to force an actor to track a spatial curve defined by a set of control points. This is frequently used for UI transitions that follow non-linear trajectories, such as arc-based navigation or decorative motion elements.

## Spring Physics and Animation Data

Spring systems provide a physically-based alternative to timed animations, where motion is driven by velocity and tension rather than fixed durations. This creates a more organic, "bouncy" interaction feel that naturally resolves to a target state.

### Physically-Based Interaction

`Dali::SpringData` encapsulates the parameters for a spring system—specifically stiffness and damping. By applying these to an [animation](./animation.md), the system simulates mass-spring-damper dynamics, ensuring that interactions feel tactile and responsive to user input.

## Devel and Integration API Reference

The Devel and Integration layers provide low-level access to the [animation](./animation.md) engine's internals, allowing for custom lifecycle management and deep integration with the [rendering](./rendering.md) pipeline.

### Lifecycle and Threading Requirements

Advanced [animation](./animation.md) extensions must respect the thread-safety contract of DALi: all property modifications should originate from the application thread, but the actual interpolation occurs on the render thread. Developers implementing custom [animation](./animation.md) behaviors must ensure that data passed to the [animation](./animation.md) system is immutable or properly synchronized to prevent race conditions during frame updates.

> Warning: Direct manipulation of the render-side [animation](./animation.md) queue is restricted to the integration layer. Improper usage can lead to engine crashes or desynchronization between the scene graph and the rendered output.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animation)
