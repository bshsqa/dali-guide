---
id: math
title: "math"
sidebar_label: "math"
---
## Introduction to DALi Math

The DALi Math module provides a comprehensive suite of tools for handling 2D and 3D spatial operations, coordinate transformations, and geometric primitives essential for UI development. It forms the backbone of the DALi scene graph, enabling precise manipulation of objects within the application's view space.

You should use this module whenever your application requires spatial calculations, such as setting element positions, defining transformation matrices, calculating rotations, or converting between units. Its distinctiveness lies in its deep integration with the DALi [rendering](./rendering.md) pipeline, providing highly optimized types that ensure consistency across the actor hierarchy and scene layout.

## Geometric Vectors and Rectangles

Vectors and Rectangles serve as the fundamental data structures for defining position, size, and boundary constraints within the DALi coordinate system. These classes provide the basic building blocks for layout calculations and element placement.

### Vector2, Vector3, and Vector4
Vectors are used extensively to represent points, scales, and color components (RGBA). Developers use these classes whenever an actor needs to have its spatial properties modified, such as in `Actor::TranslateBy` or `Actor::ScaleBy`.

```cpp
// Example: Translating an actor using Vector3
Dali::Actor myActor = Dali::Actor::New();
Dali::Vector3 translation(10.0f, 20.0f, 0.0f);
myActor.TranslateBy(translation);
```

### Rect
The Rect class defines a rectangular area, typically used for hit-testing, clipping, or bounding box calculations. It is a utility type for managing four coordinate values representing an origin (x, y) and dimensions (width, height).

> Note: While many properties in DALi use `Vector3` for size, `Rect` is the primary interface for specialized geometric bounds handling.

## Angular Conversions

Managing rotations in DALi requires precision when defining angular units. The framework distinguishes between `Degree` and `Radian` types to prevent ambiguity during rotational operations.

### Degree and Radian
Use `Degree` or `Radian` to specify rotation magnitudes. Methods like `Actor::RotateBy` accept either type, allowing developers to choose the representation that best fits their design requirements.

```cpp
// Example: Rotating an actor using Degrees
Dali::Actor myActor = Dali::Actor::New();
Dali::Degree angle(45.0f);
Dali::Vector3 axis(0.0f, 0.0f, 1.0f);
myActor.RotateBy(angle, axis);
```

## Matrices and Quaternions

Transformations are processed using linear algebra structures. `Matrix` and `Quaternion` enable complex 3D manipulations, such as sophisticated scene transitions or camera movements.

### Matrix and Quaternion
`Matrix` represents a 4x4 transformation matrix, while `Quaternion` provides a robust, gimbal-lock-free method of handling 3D rotations. You should utilize `Quaternion` for animating objects in 3D space, subsequently applying them to actors via `Actor::RotateBy(const Quaternion &)`.

```cpp
// Example: Using Quaternion for rotation
Dali::Actor myActor = Dali::Actor::New();
Dali::Quaternion orientation(Dali::Degree(90.0f), Dali::Vector3::XAXIS);
myActor.RotateBy(orientation);
```

## Utility and Compile-Time Math

The math module provides a variety of constants and constexpr-friendly utilities for high-performance applications. These helpers allow for the pre-calculation of geometric values, reducing the overhead during critical UI update cycles.

## Random Number Generation

Deterministic and non-deterministic random number generation is essential for creating dynamic visual effects or variations in UI behavior. These utilities provide uniform distribution helpers to ensure that effects like particle movement or color randomization remain performant and statistically sound.

## Integer Pair Utilities

For operations involving discrete values, such as grid indexing or pixel-perfect coordinate snapping, the framework provides specialized pair types.

### IntPair, Int32Pair, and Uint16Pair
These structures hold two integer components. They are primarily used in scenarios where float-based vectors would introduce rounding errors or where the coordinate space is strictly constrained to integer steps, such as texture coordinates or cell-based layout systems.

```cpp
// Example: Handling discrete coordinates
struct Int32Pair {
    int32_t x;
    int32_t y;
};

// IntPair usage is optimized for performance in layout iteration
```

→ See: [Dali::Actor]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/math)
