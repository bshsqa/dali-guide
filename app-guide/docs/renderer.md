---
id: renderer
title: "Renderer"
sidebar_label: "Renderer"
---
## Introduction to CanvasRenderer

The `CanvasRenderer` is the primary interface for procedural vector drawing in DALi, providing a high-performance mechanism to render custom shapes, paths, and gradients directly onto a DALi Actor. You should use `CanvasRenderer` when your application requires dynamic, resolution-independent vector graphics—such as complex icons, data visualizations, or custom animated UI components—that cannot be satisfied by static [images](./images.md). Unlike standard `Visuals`, the `CanvasRenderer` allows for granular, per-frame control over vector paths and styles, making it the distinct choice for complex procedural [rendering](./rendering.md).

→ See: [Visuals]

## Managing Drawable Groups

`DrawableGroup` serves as the container for individual vector elements, allowing you to organize, batch, and manipulate collections of shapes as a single logical unit. Use this to maintain scene structure when dealing with complex vector illustrations composed of multiple paths and styles.

### Adding and Removing Drawables
The `DrawableGroup` maintains an internal list of `Drawable` objects. You can dynamically manage this list to [update](./update.md) the visual state of your canvas.

* **Add**: Appends a `Drawable` to the group.
* **Remove**: Removes a specific `Drawable` instance from the group.
* **Clear**: Removes all `Drawable` objects from the group.

```cpp
// Example: Creating a group and adding a circle
auto group = Canvas::DrawableGroup::New();
auto circle = Canvas::Drawable::New(Canvas::Shape::Circle(50.0f));

group.Add(circle);

// To remove:
group.Remove(circle);
```

## Applying Geometric Transformations

Transformations allow you to modify the coordinate space of a `Drawable` or `DrawableGroup` without changing the underlying path definition. You can apply translation, rotation, and scaling to achieve complex animations or layout adjustments.

### Applying Matrices and Transforms
By applying a `Matrix`, you can define precise spatial transformations. Transformations are inherited down the tree if applied to a `DrawableGroup`.

```cpp
auto group = Canvas::DrawableGroup::New();
// Apply a rotation of 45 degrees
group.SetTransform(Dali::Matrix::NewRotation(Dali::Degree(45.0f), Dali::Vector3::ZAXIS));
```

> Note: Transformations applied to a `DrawableGroup` affect all children within that group, providing an efficient way to animate multiple vector elements simultaneously.

## Styling and Path Configuration

Styling properties define how a path is visually represented on the screen, including how the interior is filled and how the edges are stroked. By configuring these attributes, you can create varied visual textures and line styles.

### Setting Stroke and Fill
* **FillRule**: Determines how the interior of a complex path is calculated (e.g., Even-Odd or Non-Zero).
* **StrokeCap**: Defines the shape of the end of a stroke (e.g., Round, Butt, Square).
* **StrokeJoin**: Defines the shape of the corner where two lines meet (e.g., Miter, Round, Bevel).

```cpp
auto path = Canvas::Drawable::New(myPathData);
path.SetFillRule(Canvas::FillRule::EVEN_ODD);
path.SetStrokeCap(Canvas::StrokeCap::ROUND);
path.SetStrokeJoin(Canvas::StrokeJoin::MITER);
```

## Controlling Visual Opacity

Opacity management allows for the gradual fading of vector elements, which is essential for smooth UI transitions and blending effects. 

### Adjusting Opacity
The `SetOpacity` method accepts a float value between 0.0 (fully transparent) and 1.0 (fully opaque). This value is applied to the entire `Drawable` or `DrawableGroup`.

```cpp
auto circle = Canvas::Drawable::New(Canvas::Shape::Circle(100.0f));
// Set to 50% opacity
circle.SetOpacity(0.5f);
```

## Implementing Path Commands

Path commands are the core building blocks used to define custom vector shapes through a series of geometric instructions. By using `PathCommandType`, you can construct arbitrary shapes ranging from simple geometric primitives to complex free-form illustrations.

### Defining Paths
* **MoveTo**: Moves the current pen position to a coordinate without drawing.
* **LineTo**: Draws a straight line from the current position to a new coordinate.
* **CubicTo**: Draws a cubic Bézier curve using two control points.

```cpp
auto path = Canvas::Path::New();
path.MoveTo(0.0f, 0.0f);
path.LineTo(100.0f, 0.0f);
path.CubicTo(150.0f, 50.0f, 150.0f, 100.0f, 100.0f, 100.0f);
path.Close();

auto drawable = Canvas::Drawable::New(path);
```

> Warning: Complex paths with thousands of commands may impact [rendering](./rendering.md) performance. Always simplify paths where possible for high-frequency [animation](./animation.md) scenarios.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/renderer)
