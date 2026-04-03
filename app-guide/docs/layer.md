---
id: layer
title: "Layer"
sidebar_label: "Layer"
---
## Introduction to Layering

A `Layer` is a specialized actor that provides an independent [rendering](./rendering.md) hierarchy and Z-order management for your UI. Unlike standard containers, layers allow you to segregate your scene graph into distinct planes, making it easier to manage complex overlays, background decorations, or distinct UI modules without manually adjusting the Z-index of individual child actors.

You should use `Layer` whenever you need to ensure specific UI content consistently renders on top of or behind other parts of your interface, regardless of the hierarchy depth within those sections. It is particularly effective for modal dialogs, status bars, and distinct input-handling planes.

→ See: [Actor]

## Creating and Adding Layers

Creating a `Layer` follows the standard handle-based pattern used throughout DALi. Once instantiated, a layer can be added to the stage or another container, serving as a container for your visual elements while establishing its own Z-coordinate space.

### New()
`Layer::New()` creates a new, empty layer instance. You should call this static method to initialize a layer before adding it to the scene graph.

```cpp
#include <dali/dali.h>

using namespace Dali;

void CreateUI(Stage stage)
{
    // Create a new layer
    Layer myLayer = Layer::New();
    
    // Set layer dimensions to fill the stage
    myLayer.SetSize(stage.GetSize());
    
    // Add to the stage so it becomes part of the rendering tree
    stage.Add(myLayer);
}
```

## Controlling Z-Order and Depth

Managing the visual stacking order is the primary function of a `[Layer](./layer.md)`. DALi provides methods to explicitly reorder the layers, ensuring that specific sections of your UI maintain their relative priority.

### Raise() and Lower()
`Raise()` moves the layer above its sibling layers, while `Lower()` moves it below. These methods are essential for managing dynamic UI states, such as bringing a modal overlay to the front of the screen.

```cpp
void BringToFront(Layer overlayLayer)
{
    // Moves the layer to the top of its current parent's child list
    overlayLayer.Raise();
}

void SendToBack(Layer backgroundLayer)
{
    // Moves the layer to the bottom of its current parent's child list
    backgroundLayer.Lower();
}
```

> Note: `Raise` and `Lower` only affect the ordering relative to immediate siblings within the same parent container.

## Managing Layer Visibility and Sensitivity

Layers provide granular control over how they interact with both the rendering pipeline and the user's input. You can independently toggle whether a layer is drawn or whether it intercepts touch events.

### Visibility and Touch Sensitivity
By toggling visibility, you can hide entire sections of your UI efficiently. Sensitivity determines whether the layer and its descendants act as targets for touch events.

```cpp
void ConfigureLayerInteraction(Layer modalLayer)
{
    // Hide the layer without destroying it
    modalLayer.SetVisible(false);
    
    // Disable touch events for this layer (e.g., while loading)
    modalLayer.SetSensitive(false);
}
```

## Layer Clipping and Behavior

Clipping allows you to constrain the visual output of a layer to its specific bounds. This is useful for preventing child actors from drawing outside the area intended for a specific component.

### Clipping Properties
When clipping is enabled, any visual content residing outside the layer's rectangular bounds is discarded. This is highly effective for scrollable areas or isolated container components.

```cpp
void EnableLayerClipping(Layer clippedLayer)
{
    // Enable the clipping property
    clippedLayer.SetClipping(true);
    
    // Define the area; outside this, content is hidden
    clippedLayer.SetSize(200.0f, 200.0f);
    clippedLayer.SetPosition(50.0f, 50.0f);
}
```

## Practical Use Cases

Layers are most powerful when used to separate the "application logic" UI from the "system" UI. Below is a pattern for creating a simple overlay, such as a full-screen notification or a modal backdrop.

### Example: Modal Overlay Pattern
This example demonstrates creating a primary application layer and a secondary modal layer that resides on top, with independent visibility management.

```cpp
void SetupAppLayers(Stage stage)
{
    // 1. The Main Content Layer
    Layer mainLayer = Layer::New();
    stage.Add(mainLayer);

    // 2. The Modal Overlay Layer
    Layer modalLayer = Layer::New();
    modalLayer.SetVisible(false); // Hidden by default
    stage.Add(modalLayer);

    // Later: Show the modal
    modalLayer.SetVisible(true);
    modalLayer.Raise(); // Ensure it is above mainLayer
}
```

> Warning: Excessive nesting of layers can impact [rendering](./rendering.md) performance. Use them to organize your high-level UI structure, not for every individual component.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/layer)
