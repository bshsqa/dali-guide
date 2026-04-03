---
id: border-visual
title: "BorderVisual"
sidebar_label: "BorderVisual"
---
## Understanding Border Visual

The `BorderVisual` is a specialized [rendering](./rendering.md) component designed to draw a solid color border around the inner bounds of a control's quad. Unlike image-based decorations, `BorderVisual` provides a lightweight, resolution-independent way to highlight elements, define focus states, or create simple framing effects without the memory overhead of external assets.

You should use `BorderVisual` when you require high-performance, sharp-edged outlines that respond dynamically to layout changes. It is distinct from other [visuals](./visuals.md) because it specifically targets internal path-tracing of the control's bounds, ensuring the border remains perfectly aligned with the parent actor's geometry regardless of size.

→ See: [[ColorVisual](./color-visual.md)]

## Defining Border Properties

Configuring a `BorderVisual` requires defining the stroke characteristics through the `Dali::Ui::BorderVisual::Property` enumeration. These properties control the visual weight, hue, and [rendering](./rendering.md) quality of the border.

### Available Properties

*   **`COLOR`**: Specifies the `Vector4` color of the border.
*   **`SIZE`**: Defines the thickness of the border in pixels (float).
*   **`ANTI_ALIASING`**: A boolean value that toggles smoothing for the border edges.

> Note: Enabling `ANTI_ALIASING` is recommended for high-contrast borders to prevent jagged edges on diagonal or thin lines, though it may incur a minor GPU cost.

```cpp
// Example: Creating a Property Map for a BorderVisual
Dali::Property::Map borderMap;
borderMap.Insert(Dali::Toolkit::Visual::Property::TYPE, Dali::Toolkit::Visual::BORDER);
borderMap.Insert(Dali::Ui::BorderVisual::Property::COLOR, Color::RED);
borderMap.Insert(Dali::Ui::BorderVisual::Property::SIZE, 5.0f);
borderMap.Insert(Dali::Ui::BorderVisual::Property::ANTI_ALIASING, true);
```

## Applying Borders to Actors

To apply a `[BorderVisual](./border-visual.md)` to a UI element, you utilize the `VisualFactory` to generate the visual object from the property map and register it with the target `Actor`.

### Attaching the Visual

The following example demonstrates how to create a `[BorderVisual](./border-visual.md)` and apply it to a `Control` using the `Visual::Add` method.

```cpp
// Create the visual using the factory
Dali::Toolkit::Visual::Base borderVisual = Dali::Toolkit::VisualFactory::Get().CreateVisual(borderMap);

// Attach the visual to a control
Dali::Toolkit::Control myControl = Dali::Toolkit::Control::New();
myControl.AddVisual(Dali::Toolkit::Control::Property::BACKGROUND, borderVisual);
```

## Dynamic Property Updates

Properties of the `[BorderVisual](./border-visual.md)` can be updated at runtime using the standard `Actor` or `Visual` property indexing system. This allows for fluid UI feedback, such as increasing the border size when a button is hovered.

### Updating via Property System

When you need to animate the border or change its state, use the `SetProperty` method on the visual instance.

```cpp
// Update the border size dynamically
myControl.SetProperty(Dali::Ui::BorderVisual::Property::SIZE, 10.0f);

// Update the border color
myControl.SetProperty(Dali::Ui::BorderVisual::Property::COLOR, Color::BLUE);
```

## Layout and Sizing Behavior

The `[BorderVisual](./border-visual.md)` renders internally to the actor's quad. This means the border occupies the space inside the actor's assigned size, effectively "insetting" the border relative to the actor's boundaries.

> Warning: Because the border is drawn inside the quad, a very thick border may overlap or obscure content rendered by other visuals attached to the same actor if they share the same space. Ensure your layout padding accounts for the `SIZE` property of the border.

## Common Use Cases and Best Practices

`[BorderVisual](./border-visual.md)` is most effective when used for state-driven UI feedback. By toggling the border property map or simply updating the `SIZE` and `COLOR` values, you can create clean, professional-looking interaction states with minimal code.

### Recommended Pattern: Focus Highlighting
Use the `[BorderVisual](./border-visual.md)` to draw a frame around a component when it receives focus, providing users with a clear visual cue for navigation.

```cpp
// Pseudo-code for focus handling
void OnFocusGained(Actor actor) {
  // Increase size for emphasis
  actor.SetProperty(Dali::Ui::BorderVisual::Property::SIZE, 4.0f);
  actor.SetProperty(Dali::Ui::BorderVisual::Property::COLOR, Color::YELLOW);
}

void OnFocusLost(Actor actor) {
  // Revert to thin/neutral state
  actor.SetProperty(Dali::Ui::BorderVisual::Property::SIZE, 1.0f);
  actor.SetProperty(Dali::Ui::BorderVisual::Property::COLOR, Color::TRANSPARENT);
}
```

*   **Performance:** Always prefer `[BorderVisual](./border-visual.md)` over adding an image of a border, as it avoids texture sampling and memory management entirely.
*   **Design:** For best results on high-density displays, ensure the `SIZE` is set to a value that scales well, or use the `ANTI_ALIASING` flag to maintain crispness.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/border-visual)
