---
id: bidirectional-support
title: "BidirectionalSupport"
sidebar_label: "BidirectionalSupport"
---
## Overview of Bidirectional Support

The `BidirectionalSupport` module is a specialized component of the `text-abstraction` framework designed to handle the complex requirements of scripts that possess mixed directionality, such as Arabic or Hebrew mixed with Latin. It provides the necessary logic to perform bidirectional (Bidi) analysis, allowing the DALi engine to render [text](./text.md) that flows both left-to-right (LTR) and right-to-left (RTL) correctly within a single visual container.

You should use `BidirectionalSupport` when building [text](./text.md)-heavy applications that must support globalized content, specifically when the layout requires automatic paragraph direction detection or reordering of characters for correct visual representation. It is distinct from standard [text](./text.md) shaping as it focuses exclusively on the logical-to-visual mapping and mirroring of characters rather than font glyph selection.

→ See: [TextAbstraction](link-to-text-abstraction-parent)

## Initialization and Lifecycle Management

Proper lifecycle management is critical for Bidi processing because the engine maintains internal metadata state for paragraphs to optimize layout calculations. Developers interact with this through the `CreateInfo` and `DestroyInfo` methods, which manage a `BidiInfoIndex`—a handle representing the processed bidirectional state of a specific [text](./text.md) block.

### Managing Bidi Information Handles

The `CreateInfo` method initializes the internal bidirectional data structures for a given paragraph, while `DestroyInfo` releases these resources.

*   **`CreateInfo`**: Creates the bidirectional context for a paragraph.
    *   `paragraph` (`const Character *const`): The input [text](./text.md) buffer.
    *   `numberOfCharacters` (`Length`): The count of characters in the buffer.
    *   `matchLayoutDirection` (`bool`): If true, forces the paragraph direction to match the container's layout.
    *   `layoutDirection` (`LayoutDirection::Type`): The explicit layout direction to use.
    *   **Returns**: A `BidiInfoIndex` used for subsequent queries.

```cpp
using namespace Dali::TextAbstraction;

// Initialize the Bidi support module
BidirectionalSupport bidi = BidirectionalSupport::Get();

// Create metadata for a paragraph
Character text[] = {'H', 'e', 'l', 'l', 'o'};
BidiInfoIndex index = bidi.CreateInfo(text, 5, true, LayoutDirection::LEFT_TO_RIGHT);

// ... perform Bidi operations ...

// Clean up to prevent memory leaks
bidi.DestroyInfo(index);
```

> Warning: Always pair `CreateInfo` with `DestroyInfo`. Failure to destroy the index will result in resource leaks within the text-abstraction engine.

## Text Analysis and Directionality Detection

Once the Bidi information is initialized, the module provides mechanisms to inspect the directionality of the processed text. This is essential for aligning the text correctly within the UI containers.

### Retrieving Directional Metadata

`GetParagraphDirection` and `GetCharactersDirection` allow the developer to query how the engine has interpreted the text based on the Unicode Bidi algorithm.

*   **`GetParagraphDirection`**: Returns the overall direction of the paragraph (true for RTL, false for LTR).
*   **`GetCharactersDirection`**: Populates an array with the directional status of each individual character.
    *   `bidiInfoIndex` (`BidiInfoIndex`): The previously created index.
    *   `directions` (`CharacterDirection *`): An output buffer to hold the directional values.
    *   `numberOfCharacters` (`Length`): The buffer size.

```cpp
// Check paragraph direction
bool isRtl = bidi.GetParagraphDirection(index);

// Get directional breakdown per character
CharacterDirection directions[5];
bidi.GetCharactersDirection(index, directions, 5);
```

## Advanced Reordering and Mirroring

Visual presentation of mixed-direction text requires converting logical indices (as they are typed or stored in memory) into visual indices (as they appear on screen) and handling character mirroring for symbols like parentheses.

### Reordering and Mirroring Methods

*   **`Reorder`**: Converts a logical sequence to a visual sequence for a specific line of text.
    *   `bidiInfoIndex`: The handle for the paragraph.
    *   `firstCharacterIndex`: The starting character index for the line.
    *   `numberOfCharacters`: Number of characters in the line.
    *   `visualToLogicalMap`: An output array to store the reordered mapping.
*   **`GetMirroredText`**: Transforms characters (like brackets) to their mirrored counterparts if the context requires it.

```cpp
CharacterIndex visualToLogicalMap[5];
bidi.Reorder(index, 0, 5, visualToLogicalMap);

// Mirror characters for RTL support
Character text[] = {'(', 'A', 'B', 'C', ')'};
CharacterDirection directions[] = { /* ... previously retrieved directions ... */ };
bidi.GetMirroredText(text, directions, 5);
```

## Integration with Text Rendering Pipelines

Integrating `[BidirectionalSupport](./bidirectional-support.md)` involves passing the results of the Reorder and Directional detection steps into the DALi Text layout engine. By utilizing the `visualToLogicalMap` generated by `Reorder`, the renderer can place glyphs at the correct X-coordinate positions even when the text flow changes direction mid-sentence.

The `[BidirectionalSupport](./bidirectional-support.md)` handle acts as a singleton-style helper available via `[BidirectionalSupport](./bidirectional-support.md)::Get()`. It is typically requested once at the start of a layout pass for a specific text-rendering object and held until the layout of that paragraph is finalized.

## Thread Safety and Operational Constraints

The `[BidirectionalSupport](./bidirectional-support.md)` API is designed to be invoked from the main thread, which is consistent with the DALi core rendering pipeline. 

> Note: While the `[BidirectionalSupport](./bidirectional-support.md)` object itself is a handle, the internal calculations performed by the `CreateInfo` and `Reorder` methods are CPU-intensive. Avoid invoking these methods inside complex animation updates or high-frequency render loops. Cache the `BidiInfoIndex` results for as long as the text string remains unchanged to maintain performance.

The lifecycle of the `[BidirectionalSupport](./bidirectional-support.md)` object is managed by the engine, and thread-safe access is guaranteed as long as operations on a single `BidiInfoIndex` are not performed concurrently from multiple threads.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/bidirectional-support)
