---
id: bitmap-font
title: "BitmapFont"
sidebar_label: "BitmapFont"
---
## Introduction to Bitmap Font Module

The `BitmapFont` structure is a specialized component within the `Dali::TextAbstraction` layer designed for high-performance UI requirements where pre-rendered, pixel-perfect [text](./text.md) is mandatory. Unlike vector-based fonts that undergo rasterization at runtime, `BitmapFont` allows developers to provide an explicit collection of glyphs derived from image assets, making it ideal for retro-style gaming interfaces, custom system icons, or resource-constrained environments where the CPU cost of glyph shaping and rasterization must be avoided.

By utilizing `BitmapFont`, you bypass the standard font-shaping pipeline, resulting in deterministic layout performance and exact visual fidelity. This module is distinct from standard system fonts because it requires the developer to manage the mapping of character data to source regions manually.

→ See: [FontClient](https://tizen.org) for standard vector-based [text](./text.md) handling.

## Data Structure and Glyph Representation

The `BitmapFont` struct acts as a container for character-to-image mapping data, providing the engine with the necessary indices to reconstruct [text](./text.md) from image atlases. The core of this representation is the `glyphs` variable, which stores the collection of individual glyph definitions that the [text](./text.md)-engine uses to look up texture coordinates.

### Managing the Glyphs Collection
The `glyphs` member is a public collection that must be populated before passing the `BitmapFont` [object](./object.md) to the [text](./text.md) [rendering](./rendering.md) pipeline. Each entry in this collection defines a mapping from a character code to a specific rectangle in your font texture atlas.

```cpp
#include <dali/devel-api/text-abstraction/bitmap-font.h>

void SetupBitmapFont() {
    Dali::TextAbstraction::BitmapFont myFont;
    myFont.name = "CustomRetroFont";
    
    // Populate the glyphs collection with defined character mappings
    // myFont.glyphs = ... // Implementation specific: collection of glyph indices/regions
}
```

> Note: Ensure that every glyph index referenced in your text strings exists within the `glyphs` collection to prevent rendering errors or empty spaces in the text layout.

## Metrics and Typographic Layout

Accurate typographic metrics are essential for the DALi text-engine to calculate baselines, line spacing, and decoration placement correctly. The `[BitmapFont](./bitmap-font.md)` structure exposes fields to define the vertical boundaries and decoration properties of the font face.

### Defining Vertical Metrics and Decorations
The `ascender` and `descender` fields define the vertical extent of the font, while `underlinePosition` and `underlineThickness` allow the engine to draw underlines consistently without requiring external styling data.

```cpp
Dali::TextAbstraction::BitmapFont font;

// Ascender: the height above the baseline (e.g., top of 'h')
font.ascender = 24; 

// Descender: the depth below the baseline (e.g., bottom of 'g')
font.descender = 6; 

// Underline settings in pixels
font.underlinePosition = 2;
font.underlineThickness = 1;
```

> Warning: Incorrectly setting the `ascender` and `descender` values will lead to inconsistent line heights and broken vertical alignment when mixing this font with other UI elements. Always ensure these values represent the maximum extents of your glyph set.

## Color Font Support and Rendering

The `isColorFont` property is a boolean flag that signals the rendering pipeline to treat the bitmap assets as multi-colored, pre-shaded textures rather than monochrome masks. When `true`, the text-engine disables the standard color-tinting stage for this font to preserve the original visual attributes of the glyphs.

### Handling Multi-Colored Assets
Use this feature when your font textures contain built-in gradients, shadows, or multi-chromatic designs.

```cpp
Dali::TextAbstraction::BitmapFont font;

// Enable this to preserve the original colors in your image atlas
font.isColorFont = true; 
```

> Note: If `isColorFont` is set to `false`, the engine will assume a monochrome texture and apply the text's foreground color property as a multiplier (tint).

## Lifecycle Management and Object Instantiation

The `[BitmapFont](./bitmap-font.md)` struct follows standard C++ value-semantics, meaning it is designed for stack allocation or integration into custom manager classes. It provides a default constructor and destructor to handle the lifecycle of the underlying data structures.

### Instantiation
The default constructor initializes the member variables to safe, empty defaults. There is no complex initialization required; simply instantiate and populate.

```cpp
{
    // The scope-bound lifecycle
    Dali::TextAbstraction::BitmapFont font;
    
    // Configure members...
    font.name = "MainBitmapFont";
    
    // Object is destroyed automatically when leaving scope
}
```

## Integration Patterns

Integrating a `[BitmapFont](./bitmap-font.md)` into the broader DALi text ecosystem involves configuring the structure and passing it to the text-abstraction modules responsible for loading resources. Because `[BitmapFont](./bitmap-font.md)` is a simple struct, it acts as a Data Transfer Object (DTO) that your application logic fills out before passing it down the pipeline.

### Integration Workflow
1. Create the `[BitmapFont](./bitmap-font.md)` instance.
2. Define the `name` for identification.
3. Calculate and set the `ascender`, `descender`, and `underline` metrics.
4. Populate the `glyphs` collection.
5. Pass the object to the rendering system (often via an integration-layer FontProvider).

```cpp
#include <dali/devel-api/text-abstraction/bitmap-font.h>

Dali::TextAbstraction::BitmapFont CreateMyFont() {
    Dali::TextAbstraction::BitmapFont font;
    font.name = "MyBitMap";
    font.ascender = 30;
    font.descender = 10;
    font.underlinePosition = 5;
    font.underlineThickness = 2;
    font.isColorFont = false;
    
    return font;
}
```

> Note: The `[BitmapFont](./bitmap-font.md)` [object](./object.md) should be treated as an immutable configuration once it has been handed off to the [text](./text.md)-engine; modifying it after the engine has begun processing can lead to unpredictable layout results.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/bitmap-font)
