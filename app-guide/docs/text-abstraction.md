---
id: text-abstraction
title: "text-abstraction"
sidebar_label: "text-abstraction"
---
## Introduction to Text Abstraction

Text Abstraction in DALi provides a unified, hardware-accelerated interface for font management, [text](./text.md) shaping, and complex script [rendering](./rendering.md). It acts as the foundational layer between the high-level UI [text](./text.md) components and the underlying OS font [rendering](./rendering.md) engines.

Developers should use [text-abstraction](./text-abstraction.md) when building custom UI controls that require precise control over typography, font fallback logic, or localized [text](./text.md) layout that standard controls do not provide. Unlike basic [text](./text.md) labels, this layer allows for manual glyph inspection, shaping analysis, and granular management of font assets.

## Sub-Components Overview

The Text Abstraction framework is composed of specialized modules designed to decouple font discovery from the actual [rendering](./rendering.md) of [text](./text.md) glyphs.

* **Font Client**: The primary interface for querying system fonts, obtaining font metrics, and resolving character-to-glyph mappings. → See: [[FontClient](./font-client.md)]
* **Bidirectional Support**: Handles the complex logic required for right-to-left (RTL) scripts, including character mirroring and reordering. → See: [[BidirectionalSupport](./bidirectional-support.md)]
* **Bitmap Font**: Provides mechanisms for handling non-vector font formats, often used for performance-critical UI elements or stylized [text](./text.md). → See: [[BitmapFont](./bitmap-font.md)]
* **Font File Manager**: Manages the loading, caching, and lifecycle of font files available to the application. → See: [[FontFileManager](./font-file-manager.md)]

## Managing Fonts and Typography

Efficient typography management is essential for maintaining a consistent UI across different locales and screen densities. This section covers how to interact with the system's font registry to retrieve styles and metrics.

### Querying Font Metrics
To ensure proper [text](./text.md) alignment and baseline positioning, you must retrieve vertical and horizontal metrics from the font client.

* **What**: These methods return the ascent, descent, and height of a specific font face.
* **Why**: Use these to calculate line heights and baseline offsets manually when performing custom layout calculations.
* **How**: Provide the `FontDescription` to identify the typeface and receive a `FontMetrics` structure containing the geometric properties.

> Note: Relying on hardcoded pixel values will lead to layout failures on high-DPI displays; always query the `FontClient` for dynamic metrics.

## Processing Text [Layout](./layout.md) and Shaping

Text shaping is the process of converting a string of characters into a sequence of glyphs with correct positioning, kerning, and ligatures.

### Shaping Complex Scripts
The framework supports the shaping of complex scripts (such as Arabic or Indic) by interpreting [text](./text.md) flow and applying contextual substitutions.

* **What**: Performs [text](./text.md) shaping based on current language, script, and writing direction.
* **Why**: Necessary for [rendering](./rendering.md) languages where character appearance changes based on surrounding characters.
* **How**: Pass the [text](./text.md) buffer and locale information to the shaping engine. It returns a collection of glyph indices and their corresponding offsets in the [text](./text.md) stream.

## Handling Specialized Text Features

Advanced typography requires handling exceptions like line breaks, hyphenation, and specific character [rendering](./rendering.md) rules.

### Hyphenation and Emoji
Text Abstraction provides [utility](./utility.md) methods to identify valid hyphenation points and to handle multi-color emoji sequences.

* **What**: Determines breakable points within words and identifies emoji presentation styles.
* **Why**: Improves readability in narrow containers (e.g., sidebars) by breaking long words correctly according to locale rules.
* **How**: Input the string and language tag to receive a list of indices where hyphenation or breaks are permitted.

## Best Practices for Text Performance

Text operations can be resource-intensive; therefore, proper management of the font cache and layout data is critical for 60 FPS performance.

### Caching Strategy
* **Pre-loading**: If your application uses specific custom fonts, ensure they are registered with the `FontFileManager` during the application initialization phase to avoid runtime stalls.
* **Glyph Caching**: The `FontClient` maintains an internal cache of rendered glyphs. Avoid frequently switching font properties or changing sizes on every frame, as this invalidates cache entries and forces expensive re-rasterization.
* **[Layout](./layout.md) Recycling**: Re-use layout objects whenever possible, particularly when updating [text](./text.md) content within a scrolling list or dynamic UI.

> Warning: Excessive calls to font retrieval methods inside the `OnUpdate` or `OnLayout` methods of a custom control will significantly degrade frame performance. Always cache metrics locally within your control instance.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/text-abstraction)
