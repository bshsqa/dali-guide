---
id: text-abstraction
title: "text-abstraction"
sidebar_label: "text-abstraction"
---
## Introduction to Text Abstraction

The `text-abstraction` module acts as the foundational layer within the DALi GUI framework for processing, analyzing, and preparing [text](./text.md) for [rendering](./rendering.md). It provides a platform-agnostic interface that decouples high-level UI controls from the underlying complexities of font file parsing, glyph shaping, and bidirectional [text](./text.md) analysis.

Developers should utilize this module when building custom [text](./text.md) [rendering](./rendering.md) components or extending the framework's typography capabilities. It is distinct because it operates as a bridge between raw character streams and the geometric glyph data required by the GPU, ensuring that complex tasks like RTL (Right-to-Left) reordering and bitmap font loading are handled consistently across different hardware targets.

## Architecture and Internal Design

The [text](./text.md) stack in DALi follows a modular design where `text-abstraction` serves as the primary provider for typographic information. It abstracts away the platform-specific font libraries, providing a unified set of structures and classes that the layout engine consumes to compute [text](./text.md) geometry.

The architecture centers on the `FontClient` for scalable vector font operations and the `BidirectionalSupport` class for algorithmic [text](./text.md) reordering. These components communicate with the integration layer to fetch character metrics, glyph bitmaps, and layout path transformations, ensuring that the [rendering](./rendering.md) loop receives valid and optimized data for every frame.

## Threading and Lifecycle Considerations

Text operations within DALi are generally performed on the main thread, especially when interacting with the `FontClient` cache. While the `text-abstraction` module provides the structures for data, font loading and cache management should be treated as heavy, potentially blocking operations that may impact frame budget if performed excessively during the render loop.

> Warning: Frequent initialization and destruction of [text](./text.md) resources (such as `BidirectionalSupport` info) should be minimized. Always ensure that `DestroyInfo` is called when `BidiInfoIndex` is no longer required to prevent memory leaks in the abstraction layer.

## Sub-Components Overview

The `text-abstraction` module is comprised of specialized sub-components, each handling a unique facet of [text](./text.md) processing:

*   **Font Client**: Manages font file loading, metrics, and glyph cache operations. → See: [[FontClient](./font-client.md)]
*   **Bidirectional Support**: Provides algorithms for detecting and reordering RTL/LTR [text](./text.md) and character mirroring. → See: [[BidirectionalSupport](./bidirectional-support.md)]
*   **Bitmap Font**: Defines structures for custom bitmap-based typography, including glyph metrics and texture references. → See: [[BitmapFont](./bitmap-font.md)]
*   **Font File Manager**: Handles internal tracking and lifecycle of font file handles. → See: [[FontFileManager](./font-file-manager.md)]

## Resource Management and Integration

Effective integration requires careful handling of glyph metrics and visual data. The framework uses handles to manage these resources efficiently, ensuring that even when complex transformations (such as circular [text](./text.md) paths) are applied, the memory overhead remains predictable.

### Using Bidirectional Support

The `BidirectionalSupport` class provides the logic necessary for [rendering](./rendering.md) complex scripts that require reordering. This is critical for support of languages like Arabic or Hebrew, which flow from right to left.

To use this, you create a handle, generate the bidirectional information for your [text](./text.md), and then use that info to reorder the character sequence into a visual-to-logical mapping.

```cpp
#include <dali/devel-api/text-abstraction/bidirectional-support.h>

void SetupBidi(const Dali::TextAbstraction::Character* text, Dali::Length length) {
  using namespace Dali::TextAbstraction;
  
  // Create a handle to the bidirectional support instance
  BidirectionalSupport bidi = BidirectionalSupport::New();
  
  // Generate Bidi info for the paragraph
  BidiInfoIndex bidiIndex = bidi.CreateInfo(text, length, true, LayoutDirection::LEFT_TO_RIGHT);
  
  // Reorder the text for visual output
  std::vector<CharacterIndex> visualMap(length);
  bidi.Reorder(bidiIndex, 0, length, visualMap.data());
  
  // Always clean up to prevent resource leaks
  bidi.DestroyInfo(bidiIndex);
}
```

### Configuring Bitmap Fonts

When defining a custom bitmap font, you must populate the `[BitmapFont](./bitmap-font.md)` structure and define the individual `BitmapGlyph` entities. This is useful for custom stylized fonts or icon sets that do not use standard vector formats.

```cpp
#include <dali/devel-api/text-abstraction/bitmap-font.h>

void CreateCustomFont() {
  using namespace Dali::TextAbstraction;
  
  BitmapFont myFont;
  myFont.name = "CustomIconFont";
  myFont.ascender = 20.0f;
  myFont.descender = 5.0f;
  
  // Define a specific glyph within the font
  BitmapGlyph glyph("path/to/glyph.png", 'A', 20.0f, 5.0f);
  
  // Integrate into the system via the FontClient (usage depends on FontClient implementation)
}
```

### Circular Text Parameters

For advanced UI effects, you can transform text along a circular path using `CircularTextParameters`. These parameters define the geometry of the circle, allowing the layout engine to calculate the correct rotation and position for each glyph in the text string.

```cpp
#include <dali/devel-api/text-abstraction/circular-text-parameters.h>

void ConfigureCircularLayout() {
  Dali::TextAbstraction::CircularTextParameters params;
  params.centerX = 100.0f;
  params.centerY = 100.0f;
  params.radius = 50.0f;
  params.invRadius = 1.0f / 50.0f;
  params.beginAngle = 0.0f;
  params.isClockwise = true;
  params.synthesizeItalic = false;
  
  // The layout engine utilizes these params to modify glyph vertex data
}
```

> Note: When setting `invRadius`, ensure `radius` is not zero to avoid division by zero errors. The `CircularTextParameters` structure is a helper for vertex transformation and does not store the [text](./text.md) data itself.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/text-abstraction)
