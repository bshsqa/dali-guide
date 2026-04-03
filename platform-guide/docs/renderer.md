---
id: renderer
title: "Renderer"
sidebar_label: "Renderer"
---
## Introduction to CanvasRenderer

`CanvasRenderer` is a specialized high-level vector drawing engine within the DALi framework designed for programmatic construction and manipulation of 2D graphical compositions. It serves as a container for `Drawable` objects, enabling complex vector [rendering](./rendering.md) that can be transformed, masked, and composed as a unified scene element within the larger DALi [rendering](./rendering.md) pipeline.

You should choose `CanvasRenderer` when your application requires resolution-independent vector shapes, dynamic gradient fills, or complex clipping/masking operations that are not easily achieved via standard static textures or basic DALi actors. It is distinct from other [rendering](./rendering.md) variants by providing a persistent, [object](./object.md)-oriented representation of vector geometry that persists across frames while allowing fine-grained control over individual drawing primitives.

## [Renderer](./renderer.md) Architecture and Component Hierarchy

The `CanvasRenderer` architecture relies on a composite pattern where the renderer acts as a root container, managing a tree of `Drawable` objects. The fundamental unit of organization is the `DrawableGroup`, which allows developers to treat a collection of shapes, gradients, and pictures as a single atomic unit.

### Drawable Entities
All graphical elements in the renderer derive from the base `Drawable` class. This base handles global attributes such as opacity and spatial transformations that are applied to all inherited primitives.

> Note: `CanvasRenderer` itself is initialized with a `Vector2` viewBox, which defines the coordinate system bounds for the internal contents.

```cpp
// Creating the root CanvasRenderer
Dali::CanvasRenderer renderer = Dali::CanvasRenderer::New(Dali::Vector2(800.0f, 600.0f));

// Creating a group to manage sub-elements
Dali::CanvasRenderer::DrawableGroup group = Dali::CanvasRenderer::DrawableGroup::New();
```

## Managing Drawing Operations

Managing the content of a `CanvasRenderer` involves dynamically registering and unregistering `Drawable` objects to the `DrawableGroup`. The renderer maintains the lifecycle of these drawables, ensuring that the scene state remains consistent with the registered objects.

### Registering and Clearing Drawables
The `DrawableGroup` acts as the primary registry for renderable entities. When you add a drawable using `AddDrawable`, the object becomes part of the rendering traversal list.

*   **AddDrawable(Drawable &drawable)**: Registers a drawable to the group. Returns `true` if successful.
*   **RemoveDrawable(Drawable drawable)**: Removes a specific drawable from the group.
*   **RemoveAllDrawables()**: Clears the group entirely.

```cpp
Dali::CanvasRenderer::DrawableGroup group = Dali::CanvasRenderer::DrawableGroup::New();
Dali::CanvasRenderer::Shape myShape = Dali::CanvasRenderer::Shape::New();

// Add the shape to the group
if (group.AddDrawable(myShape)) {
    // Successfully added
}

// Remove the shape later
group.RemoveDrawable(myShape);
```

## Transformations and Coordinate Spaces

Every `Drawable` entity supports affine transformations, allowing you to manipulate content without modifying the underlying path geometry. These transformations are cumulative within groups.

### Applying Transformations
Transformations are applied to the drawable's local coordinate space. Changes such as `Rotate`, `Scale`, and `Translate` update the internal matrix of the drawable.

```cpp
Dali::CanvasRenderer::Drawable drawable = ...; // Existing drawable

// Apply transformation
drawable.Rotate(Dali::Degree(45.0f));
drawable.Scale(2.0f);
drawable.Translate(Dali::Vector2(100.0f, 50.0f));

// Retrieve original bounds before transformation
Dali::Rect<float> bounds = drawable.GetBoundingBox();
```

> Warning: `GetBoundingBox()` returns the area of the drawable in its local coordinate space *before* any transformations are applied.

## Styling and Rendering Attributes

The renderer supports advanced visual attributes, enabling detailed control over how vector paths are stroked and filled. These attributes are managed via specific enums and property setters on drawable objects.

### Visual Styling
The framework supports standard vector styling properties:
*   **StrokeCap**: Determines the style of line endings (`StrokeCap` enum).
*   **StrokeJoin**: Determines the style of path connections (`StrokeJoin` enum).
*   **FillRule**: Determines how self-intersecting paths are filled (`FillRule` enum).
*   **Opacity**: Managed via `SetOpacity(float)` and `GetOpacity()`.

```cpp
// Managing opacity
drawable.SetOpacity(0.8f);
float currentOpacity = drawable.GetOpacity();
```

### Gradients
Gradients are specialized drawables that support color stops. Use `SetColorStops` to define the progression of colors across the gradient geometry.

```cpp
Dali::CanvasRenderer::LinearGradient gradient = Dali::CanvasRenderer::LinearGradient::New();
Dali::CanvasRenderer::ColorStops stops; 
// ... populate stops ...
gradient.SetColorStops(stops);
gradient.SetSpread(Dali::CanvasRenderer::Spread::PAD);
```

## Path Construction and Masking

Vector paths are defined using the `PathCommandType` enumeration, which categorizes the geometry instructions. Beyond simple drawing, the renderer provides sophisticated clipping and masking capabilities to create complex silhouettes.

### Masking and Clipping
Masking blends two drawables, while clipping restricts the rendering of a drawable to the intersection of its path and a clip path.

*   **SetClipPath(Drawable &clip)**: Restricts pixels of the calling drawable to the area defined by the `clip` drawable.
*   **SetMask(Drawable &mask, MaskType type)**: Applies a mask effect defined by `MaskType` using the provided `mask` drawable.

```cpp
Dali::CanvasRenderer::Drawable shape = ...;
Dali::CanvasRenderer::Drawable mask = ...;

// Apply a mask to a shape
shape.SetMask(mask, Dali::CanvasRenderer::MaskType::ALPHA);
```

## Thread Safety and Integration Guidelines

The `CanvasRenderer` is designed for use within the DALi Main Thread. Because it modifies the rendering scene graph, all `AddDrawable`, `RemoveDrawable`, and property update operations must occur on the thread where the `CanvasRenderer` was created (typically the main application thread).

> Warning: Do not attempt to modify `CanvasRenderer` objects from worker threads. Doing so may lead to race conditions in the rendering pipeline and cause undefined behavior or application crashes.

### Performance Best Practices
1.  **Batching**: Group static drawables within a single `DrawableGroup` to minimize state changes in the renderer.
2.  **Memory**: Use `RemoveAllDrawables()` when transitioning scenes to ensure that discarded drawables are properly cleaned up by the engine, though note that the engine handles the underlying native memory of removed objects.
3.  **Transformations**: Prefer using the `Transform(const Dali::Matrix3 &matrix)` method for complex affine combinations rather than calling individual `Scale`, `Rotate`, and `Translate` methods sequentially to reduce internal matrix recalculations.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/renderer)
