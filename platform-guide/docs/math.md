---
id: math
title: "math"
sidebar_label: "math"
---
## Overview of the DALi Math Module

The DALi Math module provides a comprehensive suite of high-performance geometric, rotational, and numerical primitives essential for 3D graphics [rendering](./rendering.md) and scene graph manipulation. It is designed to minimize computational overhead by providing optimized C++ structures that interface directly with the engine's hardware-accelerated transformation pipelines.

Developers should utilize these primitives for all spatial operations within a DALi application, including [object](./object.md) positioning, coordinate system transformations, and procedural [animation](./animation.md) calculations. Unlike generic [math](./math.md) libraries, this module is specifically tuned for DALi’s internal memory layout, ensuring efficient data transfer to the GPU and seamless integration with the scene graph.

## Geometric Primitives and Coordinate Systems

Geometric primitives are the fundamental building blocks for spatial representation within the DALi scene graph, encompassing vectors and matrices. These types are optimized for operations frequently used when manipulating actors, such as calculating position, size, and transformation hierarchies.

### Vector2, Vector3, and Vector4
These types represent two, three, or four-dimensional floating-point components, commonly used for coordinates, sizes, and colors. In the context of the DALi scene graph, `Vector3` is the standard for representing an actor's position and size in 3D space.

*   **Usage**: Use `Vector3` when querying an actor's dimensions via `GetTargetSize()` or `GetNaturalSize()`.
*   **Parameters**: Each vector type is constructed by passing individual `float` values for each axis (x, y, z, or w).

```cpp
#include <dali/dali.h>

void Example(Dali::Actor actor) {
    // Retrieve the target size of an actor, which is a Vector3
    Dali::Vector3 size = actor.GetTargetSize();
    
    // Perform operations on the vector
    float area = size.x * size.y;
}
```

### Matrix
The `Matrix` type encapsulates a 4x4 homogeneous transformation matrix used to project objects from model space to screen space. While developers often use high-level actor methods, manual `Matrix` manipulation is required for custom shader uniforms and low-level camera operations.

## Rotational Mathematics

Rotational mathematics in DALi centers around the `Quaternion` and `AngleAxis` structures, providing a robust solution for orienting objects without the risk of gimbal lock. These primitives are preferred for animations and camera systems where smooth interpolation is required.

### Quaternion
The `Quaternion` class represents a rotation in 3D space. It is the preferred way to apply transformations to actors to ensure stable and predictable orientation changes.

*   **Applying Rotation**: Use `Actor::RotateBy(const Quaternion&)` to apply a rotation relative to the current orientation of the actor.
*   **Efficiency**: Quaternions avoid the issues associated with Euler angles, such as gimbal lock, and allow for efficient spherical linear interpolation (SLERP).

```cpp
#include <dali/dali.h>

void RotateActor(Dali::Actor actor) {
    // Create a 90-degree rotation around the Y-axis using a Quaternion
    Dali::Quaternion rotation(Dali::Radian(Dali::Degree(90.0f)), Dali::Vector3::YAXIS);
    
    // Apply the rotation relative to the current actor orientation
    actor.RotateBy(rotation);
}
```

## Compile-Time Numerical Utilities

DALi provides a suite of template-based numerical utilities designed to offload calculations from the runtime to the compile phase. By using these utilities, developers can define constant values and thresholds that do not incur performance penalties during the render loop.

*   **Epsilon**: Used to define floating-point precision thresholds, preventing precision errors during comparisons.
*   **Utility**: Avoid using raw `if` conditions with `float` equality; always compare the difference against an `Epsilon` constant.

## Angular Conversions and Utilities

Angular management is handled via the `Radian` and `Degree` wrapper classes. These classes enforce type safety, preventing common bugs caused by passing degrees into functions expecting radians or vice-versa.

### Conversion Logic
When interacting with actor methods like `RotateBy(const Degree&, const Vector3&)` or `RotateBy(const Radian&, const Vector3&)`, ensure that the angle type is explicitly wrapped.

```cpp
#include <dali/dali.h>

void ApplyRotation(Dali::Actor actor) {
    Dali::Degree angle(45.0f);
    Dali::Vector3 axis(0.0f, 1.0f, 0.0f);
    
    // Rotate actor using explicitly defined degrees
    actor.RotateBy(angle, axis);
}
```

> Note: Mixing units without using the wrapper classes is a frequent source of runtime visual artifacts. Always prefer the wrapper types over raw floats.

## Memory Layout and Type Trait Integration

The math module structures are designed with a specific memory layout compatible with standard GPU buffer formats. Each primitive is POD (Plain Old Data) compliant where possible to allow for direct `memcpy` operations when uploading data to the engine's internal resources.

### TypeTraits
The framework utilizes `TypeTraits` to enable specialized serialization. When defining custom data structures that include math primitives, ensure that `TypeTraits` are respected to maintain compatibility with the engine's internal property system.

## Randomness and Mathematical Helpers

The `Random` class and `[math](./math.md)-utils.h` headers provide essential support for procedural generation and standard floating-point arithmetic. These tools are the primary source for generating deterministic or non-deterministic values required for effects and logic.

### Randomization
Use the `Random` class to seed and generate values within specific ranges. This is critical for procedural animation offsets or particle system behavior.

*   **Usage**: The `Random` class provides methods to obtain integers or floating-point numbers across a uniform distribution.
*   **Helpers**: Check `[math](./math.md)-utils.h` for functions like `Clamp`, `Lerp`, and `SmoothStep`, which are vital for smoothing out value transitions in `OnRelayoutSignalType` or animation callbacks.

> Warning: When using `Random`, ensure the seed is initialized once at startup to avoid predictable or repeating patterns across sessions.

→ See: [Actor]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/math)
