---
id: camera-actor
title: "CameraActor"
sidebar_label: "CameraActor"
---
## Understanding [CameraActor](./camera-actor.md)

The `CameraActor` is a specialized component used to define the viewpoint from which a 3D scene is rendered. By controlling the position, orientation, and optical properties of the camera, you determine how the content within your `Dali::Ui::View` hierarchy is projected onto the 2D surface of the display.

You should use a `CameraActor` when your application requires more than the default 2D [rendering](./rendering.md) setup, such as implementing 3D transitions, adjusting depth perception, or creating custom perspective effects. While it inherits functionality from the base Actor class, its primary purpose is managing the transformation and projection matrices that dictate the scene's visual output.

## Creating and Configuring Cameras

Creating a camera involves selecting the projection mode that best suits your design goals. Whether you are building a UI with realistic depth or a flat, scale-consistent interface, `CameraActor` provides the necessary tools for both.

### Instantiation
To start, use the `New()` factory method to create an instance. You can initialize it with or without a specific canvas size to match your application's viewport requirements.

```cpp
// Creating a basic camera
Dali::CameraActor camera = Dali::CameraActor::New();

// Or creating one with specific canvas dimensions
Dali::Size canvasSize(1920.0f, 1080.0f);
Dali::CameraActor camera = Dali::CameraActor::New(canvasSize);
```

### Projection Modes
The projection mode determines how objects are scaled based on their distance from the camera. Use `SetProjectionMode` to switch between perspective and orthographic rendering.

*   **Perspective Projection**: Objects appear smaller as they move further away, simulating human vision.
*   **Orthographic Projection**: Maintains object size regardless of distance, ideal for 2D UI elements or technical views.

```cpp
// Switching to Orthographic for a consistent UI scale
camera.SetProjectionMode(Dali::Camera::ORTHOGRAPHIC_PROJECTION);

// Alternatively, for 3D depth, use perspective
camera.SetProjectionMode(Dali::Camera::PERSPECTIVE_PROJECTION);
```

## Defining the Viewing Frustum

The viewing frustum defines the volume of space that is visible to the camera. By carefully configuring the Field of View (FOV), aspect ratio, and clipping planes, you ensure that your UI elements are correctly rendered without unnecessary clipping or distortion.

### Field of View and Aspect Ratio
The FOV defines the vertical angle of the lens, affecting how much of the scene is visible. The aspect ratio ensures that the rendered content is not stretched incorrectly relative to the screen dimensions.

```cpp
// Set the field of view in radians (e.g., 45 degrees in radians)
camera.SetFieldOfView(45.0f * (3.14159f / 180.0f));

// Set aspect ratio to match your screen (width / height)
camera.SetAspectRatio(1920.0f / 1080.0f);
```

### Clipping Planes
Clipping planes define the minimum (`Near`) and maximum (`Far`) distances from the camera for which objects will be drawn. Objects outside these bounds will be culled from the render pass.

```cpp
// Only objects between 1.0 and 1000.0 units are visible
camera.SetNearClippingPlane(1.0f);
camera.SetFarClippingPlane(1000.0f);
```

> **Note**: Setting the near clipping plane too close to zero can cause Z-buffer precision issues, while setting the far plane unnecessarily high can degrade rendering performance.

## Positioning and Targeting

A `[CameraActor](./camera-actor.md)` is a node in your scene graph; its position and rotation in world space define the camera's location. You can also explicitly define a target position to orient the camera automatically.

### Setting the Viewport Focus
Use `SetTargetPosition` to define the point in 3D space that the camera should look at. This simplifies the logic for camera animations where you want the camera to "follow" a moving target.

```cpp
// Focus the camera on a specific point in the scene
Dali::Vector3 target(0.0f, 0.0f, 0.0f);
camera.SetTargetPosition(target);

// Move the camera back on the Z axis to frame the target
camera.SetPosition(0.0f, 0.0f, 500.0f);
```

## Advanced Coordinate Adjustments

Sometimes, the render output must be adjusted to account for custom coordinate systems or specific rendering backend expectations, such as Y-axis inversion.

### Y-Axis Inversion
The `SetInvertYAxis` method allows you to flip the Y-axis projection, which is useful when integrating with external libraries or platforms that utilize a coordinate system opposite to DALi's default.

```cpp
// Enable Y-axis inversion if the target render buffer requires it
camera.SetInvertYAxis(true);
```

### Direct Projection Helpers
For convenience, you can set standard projection settings using the helper methods which accept a `Size` parameter, covering most standard 2D and 3D requirements:

```cpp
Dali::Size viewport(1280.0f, 720.0f);

// Apply standard perspective settings for this size
camera.SetPerspectiveProjection(viewport);

// Apply standard orthographic settings for this size
camera.SetOrthographicProjection(viewport);
```

## CameraActor Usage Patterns

While the `[CameraActor](./camera-actor.md)` provides the projection, it must be part of the `Dali::Ui::View` architecture to function within a modern DALi application. You manage the camera as a standard actor within your view hierarchy or through your custom view controllers.

### Best Practices
1.  **Transitions**: When transitioning between views, you can animate the `Position` and `Orientation` properties of the `[CameraActor](./camera-actor.md)` to create smooth camera pans or zooms.
2.  **Multiple Cameras**: If your application requires multiple views (e.g., a "picture-in-picture" effect), ensure each `[CameraActor](./camera-actor.md)` is configured with a distinct set of projection properties.
3.  **Lifecycle**: Since `[CameraActor](./camera-actor.md)` is a handle to the engine object, always check `DownCast` results when retrieving camera instances from generic scene graph searches.

```cpp
// Example: Safely retrieving a camera and modifying it
Dali::BaseHandle handle = GetCameraFromSomeSource();
Dali::CameraActor camera = Dali::CameraActor::DownCast(handle);

if (camera)
{
    // Now safe to perform camera operations
    camera.SetFieldOfView(0.785f);
}
```

> **Warning**: Do not manually modify the `Scale` property of a `[CameraActor](./camera-actor.md)`. Camera scaling is implicitly handled by the projection mode and FOV settings; manual scaling can lead to undefined [rendering](./rendering.md) results.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/camera-actor)
