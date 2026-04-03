---
id: bitmap-font
title: "BitmapFont"
sidebar_label: "BitmapFont"
---
## Introduction to Bitmap Fonts

Bitmap fonts in DALi provide a performance-optimized alternative to vector-based fonts by using pre-rendered raster [images](./images.md) for glyphs. You should use `BitmapFont` when your application requires a distinct, stylized aesthetic—such as retro pixel art—or when you need to bypass the computational overhead of real-time vector font rasterization for simple, fixed-size [text](./text.md) elements.

Unlike standard system fonts, `BitmapFont` allows for full control over individual glyph bitmaps and custom color properties. This module is distinct because it operates on a defined set of pre-rendered assets rather than calculating contours at runtime. → See: [[FontClient](./font-client.md)]

## Defining a Bitmap Font

To utilize a bitmap font, you must instantiate the `BitmapFont` [object](./object.md) and register its identifying metadata. This foundation ensures that the [text](./text.md) layout engine can correctly reference your custom font assets when processing string data.

### Instantiating and Naming
The `BitmapFont` [object](./object.md) serves as the container for your font definition. You must provide a unique identifier that the application will use to request this font during [text](./text.md) [rendering](./rendering.md).

```cpp
#include <dali/public-api/text-abstraction/bitmap-font.h>

// Create a new instance of a BitmapFont
Dali::TextAbstraction::BitmapFont bitmapFont = Dali::TextAbstraction::BitmapFont::New();

// Set the unique identifier for the font
bitmapFont.SetFontName("MyRetroFont");
```

## Configuring Font Metrics

Accurate metrics are critical for proper vertical alignment and baseline calculations within a text layout. These properties define how lines of text interact with one another and where decorative elements like underlines are drawn.

### Setting Vertical Layout
The ascender, descender, and underline properties define the "box" in which your glyphs reside. Adjusting these values ensures that characters do not overlap incorrectly or shift unexpectedly when rendering mixed-content strings.

- `SetAscender(float ascender)`: Sets the distance from the baseline to the top of the glyphs.
- `SetDescender(float descender)`: Sets the distance from the baseline to the bottom of the glyphs.
- `SetUnderlinePosition(float position)`: Determines the vertical offset of the underline.
- `SetUnderlineThickness(float thickness)`: Sets the pixel width of the underline stroke.

```cpp
// Define the vertical footprint of the font
bitmapFont.SetAscender(14.0f);
bitmapFont.SetDescender(4.0f);
bitmapFont.SetUnderlinePosition(-2.0f);
bitmapFont.SetUnderlineThickness(1.0f);
```

## Working with Color Bitmap Fonts

The `isColorFont` property toggles the behavior of the text engine when handling multi-colored glyphs. Enabling this feature allows for the inclusion of pre-rendered, multi-chromatic icons or styled characters within your font set.

### Toggling Color Rendering
When `isColorFont` is set to `true`, the text abstraction layer preserves the original color data of the bitmaps instead of applying a single uniform color to the entire glyph.

```cpp
// Enable color support for complex glyphs
bitmapFont.SetIsColorFont(true);

// Verify current color font state
bool isColor = bitmapFont.IsColorFont();
```

> Note: Enabling color fonts may increase memory usage per glyph, as the engine must store color channel data rather than alpha masks alone.

## Managing Glyph Collections

A bitmap font is only as useful as the collection of characters it supports. The glyph collection maps specific characters to their visual bitmap representations.

### Populating the Glyphs
You must ensure that every character expected by your UI is registered within the font definition. Use the provided methods to map character codes to their respective bitmap data objects.

- `AddGlyph(const Glyph& glyph)`: Inserts a new glyph definition into the font's internal registry.

```cpp
Dali::TextAbstraction::Glyph myGlyph;
// Configure the glyph (e.g., bitmap URI, size, metrics)
myGlyph.width = 16;
myGlyph.height = 16;
myGlyph.bitmapUrl = "path/to/character_a.png";

// Add it to the font collection
bitmapFont.AddGlyph(myGlyph);
```

## Bitmap Font Best Practices

Efficient deployment of bitmap fonts requires balancing visual fidelity with application resource constraints. 

### Resource Management
*   **Consistency**: Ensure all glyphs in a single `[BitmapFont](./bitmap-font.md)` set share the same resolution and color format to avoid performance spikes during layout calculations.
*   **Asset Bundling**: Keep your bitmap assets in a dedicated, pre-loaded resource pack to minimize disk I/O when the font is first requested.
*   **Performance**: If you find the need for high-resolution scaling, consider if a vector font might be more appropriate. Use `[BitmapFont](./bitmap-font.md)` for fixed-size requirements to maintain the highest possible [rendering](./rendering.md) performance. 

> Warning: Always verify that the glyph collection is fully populated before triggering a layout pass. Missing glyphs for requested characters will result in "tofu" or empty space in the rendered output.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/bitmap-font)
