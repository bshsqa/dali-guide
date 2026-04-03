---
id: font-client
title: "FontClient"
sidebar_label: "FontClient"
---
## Introduction to [FontClient](./font-client.md)

The `FontClient` is the primary interface within the DALi `text-abstraction` layer responsible for managing system font resources, metrics, and glyph rasterization. You should use `FontClient` when your application requires low-level control over font loading, DPI-aware scaling, or when you need to inspect system-wide typography settings to maintain UI consistency.

Unlike higher-level [text](./text.md) components that handle layout automatically, `FontClient` provides the raw data and configuration hooks necessary to interface with the underlying font engine. It is distinct in its ability to manage global cache states and device-specific [rendering](./rendering.md) parameters, acting as the bridge between system typography and the DALi [rendering](./rendering.md) pipeline.

→ See: [TextLayout]

## Managing DPI and Scaling

Consistent typography across varying hardware requires precise control over DPI settings. The `FontClient` allows you to define the scale factors that dictate how logical units map to physical pixel sizes.

### GetDpi()
This method retrieves the current dots-per-inch (DPI) value configured for the font [rendering](./rendering.md) system. Using this value ensures that your [text](./text.md) remains physically consistent regardless of the specific screen density of the target device.

*   **WHAT:** Returns the current vertical and horizontal DPI settings as a `Dali::Vector2`.
*   **WHY:** Use this to manually calculate font point sizes if you are implementing custom [text](./text.md) [rendering](./rendering.md) logic outside of standard DALi controls.
*   **HOW:** Call `FontClient::Get().GetDpi()` to retrieve the current scale.
*   **CODE:**
```cpp
Dali::TextAbstraction::FontClient fontClient = Dali::TextAbstraction::FontClient::Get();
Dali::Vector2 dpi = fontClient.GetDpi();
// Output: dpi.x and dpi.y represent the screen density
```

## Configuring Global Font Defaults

Global font defaults provide a baseline for your application's typography. These settings ensure that if no specific font is requested, the system provides a sane, readable default.

### GetDefaultFontLineHeight()
This method returns the height of the default font in pixels. It is critical for calculating line spacing and vertical alignment in custom container implementations.

*   **WHAT:** Provides the height of a line of text using the system default font settings.
*   **WHY:** Useful for calculating the required height of text-holding components to prevent clipping or excessive whitespace.
*   **HOW:** Returns a `float` representing the pixel height.
*   **CODE:**
```cpp
Dali::TextAbstraction::FontClient fontClient = Dali::TextAbstraction::FontClient::Get();
float lineHeight = fontClient.GetDefaultFontLineHeight();
// Use lineHeight to set the height of your text container
```

## Optimizing Performance with Caching

Font glyphs are expensive to rasterize; caching is essential for smooth scrolling and animation. `[FontClient](./font-client.md)` provides methods to clear these caches when memory pressure increases or when locale changes occur.

### ClearGlyphCache()
This method wipes the current glyph cache, forcing the engine to re-rasterize characters on the next render pass.

*   **WHAT:** Clears the internal memory used to store rendered glyph textures.
*   **WHY:** Call this during significant locale changes (e.g., switching from Latin to CJK fonts) or to recover memory in resource-constrained environments.
*   **HOW:** Call `ClearGlyphCache()` on the `[FontClient](./font-client.md)` instance. Note that this is a heavy operation and may cause a temporary drop in frame rate.
*   **CODE:**
```cpp
Dali::TextAbstraction::FontClient fontClient = Dali::TextAbstraction::FontClient::Get();
// Perform this only when necessary, e.g., on a locale change signal
fontClient.ClearGlyphCache();
```

> **Warning:** Excessive clearing of the glyph cache will significantly degrade text rendering performance and increase CPU usage due to repeated rasterization.

## Atlas Configuration Constants

To efficiently manage GPU memory, the `[FontClient](./font-client.md)` utilizes an "atlas" system—a single large texture containing multiple individual glyphs. While these are managed internally, understanding the limits is key to performance tuning.

### Atlas Constraints
The `[FontClient](./font-client.md)` maintains global limits on the width and height of these atlases. When an application requests a glyph that exceeds the current atlas capacity, a new atlas is generated, which can cause a momentary stall in the rendering thread.

*   **Note:** These limits are platform-level details. While you can query the `[FontClient](./font-client.md)` for availability, modifying the actual atlas size parameters is restricted to system-level configurations.

## Monitoring and Debugging Font Performance

Development builds allow for detailed logging to track how your application utilizes font resources. This helps identify bottlenecks in font discovery and rendering speed.

### Performance Logging
By observing font retrieval patterns, you can optimize your application by reducing the number of different font families or weights used simultaneously.

*   **WHAT:** Enables internal tracing of font family lookups and glyph rasterization timing.
*   **WHY:** Use this during development to ensure that font-loading operations are not blocking the main thread.
*   **CODE:**
```cpp
// Example: Checking if a font family is available before use
Dali::TextAbstraction::FontClient fontClient = Dali::TextAbstraction::FontClient::Get();
bool isAvailable = fontClient.IsFontFamilyAvailable("Roboto");

if (!isAvailable) {
  // Log a warning or fallback to a standard system font
}
```

> **Note:** If you find that `[FontClient](./font-client.md)` methods are returning unexpected results, verify that your resource paths are correctly defined in the platform-level configuration files, as these take precedence over application-level defaults.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/font-client)
