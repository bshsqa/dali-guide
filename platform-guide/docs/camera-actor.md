---
id: camera-actor
title: "CameraActor"
sidebar_label: "CameraActor"
---
## Introduction to [CameraActor](./camera-actor.md)

The `CameraActor` is a specialized component within the DALi scene graph that defines the perspective from which the scene is rendered. Unlike standard visual elements, the `CameraActor` dictates the viewing frustum, projecting 3D world coordinates onto the 2D plane of the display.

Developers should utilize `CameraActor` when they need to transition from the default 2D orthographic view to complex 3D scenes, custom depth-of-field effects, or specific perspective configurations. It is distinguished from other actors by its ability to manipulate internal projection matrices, clipping planes, and target vectors, effectively acting as the "lens" of the application.

## Initialization and Factory Methods

`CameraActor` instances are created using static factory methods that initialize the [object](./object.md) within the DALi engine context. Proper initialization ensures the camera has valid default projections suitable for your target display dimensions.

### Creating a Camera
The `New()` methods serve as the primary entry points for instantiation. Use the parameterless `New()` for general purpose, or `New(const Size& size)` when the viewport dimensions are known at creation time.

```cpp
#include <dali/dali.h>
#include <dali/public-api/actors/camera-actor.h>

void CreateCamera(Dali::Size canvasSize) {
    // Basic initialization
    Dali::CameraActor myCamera = Dali::CameraActor::New(canvasSize);
    
    // 3D-specific initialization for complex scene graphs
    Dali::CameraActor my3DCamera = Dali::CameraActor::New3DCamera();
}
```

> Note: While `[CameraActor](./camera-actor.md)` is an actor, it is frequently attached to a `Dali::Ui::View` or the root actor of a specific rendering layer to define that layer's perspective.

## Configuring Projection Modes

The projection mode determines the mathematical transformation applied to objects in the scene. `Dali::Camera::PERSPECTIVE_PROJECTION` provides depth and foreshortening, while `Dali::Camera::ORTHOGRAPHIC_PROJECTION` maintains uniform object sizes regardless of distance.

### Setting Projection
Use `SetProjectionMode()` to toggle between these states. When using `PERSPECTIVE_PROJECTION`, it is essential to configure the Field of View (FOV) to control the "zoom" effect.

```cpp
// Configuring a perspective camera
myCamera.SetProjectionMode(Dali::Camera::PERSPECTIVE_PROJECTION);
myCamera.SetFieldOfView(45.0f * (M_PI / 180.0f)); // 45 degrees in radians

// Configuring an orthographic camera
myCamera.SetProjectionMode(Dali::Camera::ORTHOGRAPHIC_PROJECTION);
```

> Warning: `SetFieldOfView` expects the value in radians. Providing degrees will result in an incorrectly clipped or distorted viewing frustum.

## Managing Clipping Planes and Spatial Targets

Clipping planes define the depth range (Near and Far) within which objects are drawn by the renderer. Anything closer than the Near plane or further than the Far plane is discarded, which is critical for depth testing performance and visual accuracy.

### Defining Visibility and Target
The `SetTargetPosition` method allows you to point the camera at a specific coordinate in the world, simplifying the math required to track moving objects or rotate the view dynamically.

```cpp
// Define the visibility volume
myCamera.SetNearClippingPlane(1.0f);
myCamera.SetFarClippingPlane(1000.0f);

// Direct the camera to look at a point in 3D space
Dali::Vector3 focusPoint(0.0f, 0.0f, -500.0f);
myCamera.SetTargetPosition(focusPoint);
```

## Integration with DALi View Architecture

When using `Dali::Ui::View`, you typically manage the `[CameraActor](./camera-actor.md)` as an external configuration object that dictates how the `View` content is rendered. While the `View` handles standard UI layout, the `[CameraActor](./camera-actor.md)` provides the projection matrix for complex 3D transformations within that view.

### Handling Coordinate Systems
If your design requires the Y-axis to be inverted (common when porting from different graphics APIs or handling specific UI coordinate layouts), use `SetInvertYAxis()`.

```cpp
// Ensure coordinate system compatibility
bool needsInversion = true;
myCamera.SetInvertYAxis(needsInversion);

// Apply specific projection sizes
Dali::Size viewport(800.0f, 600.0f);
myCamera.SetPerspectiveProjection(viewport);
```

## Internal Lifecycle and Handle Management

`[CameraActor](./camera-actor.md)` uses handle-based memory management. When you copy a `[CameraActor](./camera-actor.md)` handle, you are not duplicating the underlying engine object, but rather creating an additional reference to the same camera entity.

### Type-Safe Casting
If you retrieve an actor from the scene graph (e.g., via `GetParent()`), you should use `DownCast` to safely convert the base actor handle into a `[CameraActor](./camera-actor.md)` to access camera-specific APIs.

```cpp
Dali::Actor baseActor = GetCameraActorFromScene();
Dali::CameraActor camera = Dali::CameraActor::DownCast(baseActor);

if (camera) {
    // Safe to use camera-specific methods
    float fov = camera.GetFieldOfView();
}
```

> Note: Always check the validity of the `DownCast` result before calling methods on the [object](./object.md). A failed cast will result in an empty handle, and calling methods on an empty handle will lead to an assertion failure.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/camera-actor)
