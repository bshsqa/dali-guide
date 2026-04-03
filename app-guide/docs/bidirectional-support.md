---
id: bidirectional-support
title: "BidirectionalSupport"
sidebar_label: "BidirectionalSupport"
---
## Introduction to Bidirectional Text Support

The [BidirectionalSupport](./bidirectional-support.md) module is a specialized component within the DALi [text-abstraction](./text-abstraction.md) framework designed to handle the complex layout requirements of bidirectional (BiDi) scripts, such as Arabic and Hebrew, when mixed with left-to-right (LTR) scripts like Latin. While standard [text](./text.md) [rendering](./rendering.md) processes characters in a sequential stream, this module provides the logic necessary to analyze, reorder, and mirror [text](./text.md) strings according to the Unicode Bidirectional Algorithm (UBA).

You should use this module when your application must support internationalized user interfaces where [text](./text.md) direction may change mid-paragraph or where characters require visual mirroring to maintain semantic integrity. It is distinct from standard [text](./text.md) handling because it transforms the logical character order into the visual order required for correct [rendering](./rendering.md) on the screen.

## Initializing Bidirectional Support

Initializing the bidirectional engine is the first step toward [rendering](./rendering.md) mixed-direction content accurately. You configure the support engine by defining the specific script processing requirements, such as base paragraph direction and character-level overrides.

### Setting Up the Support Instance

The `BidirectionalSupport` [object](./object.md) acts as the primary controller for all BiDi operations. You must instantiate this [object](./object.md) before performing any reordering or analysis on your [text](./text.md) buffers.

> Note: Platform-level detail regarding underlying [text](./text.md) shaping engines may influence the behavior of the initialized instance; refer to the platform guide for specific locale-based configuration requirements.

```cpp
// Initialize the bidirectional support module
Dali::TextAbstraction::BidirectionalSupport bidiSupport = Dali::TextAbstraction::BidirectionalSupport::Get();

// The support instance is now ready to process text strings
```

## Reordering Text for Display

The core functionality of this module is the conversion of logical text (the order in which characters are stored in memory) into visual text (the order in which they appear on the display). This process ensures that punctuation, numbers, and mixed-language phrases are rendered in their correct visual sequence.

### Using the Reorder Method

The `Reorder` method analyzes a provided logical string and generates an array of indices representing the visual display order.

*   **WHAT:** This method calculates the visual mapping for a logical string based on the Unicode Bidirectional Algorithm.
*   **WHY:** Use this when you have raw string data and need to determine the correct order of glyphs to be passed to the text shaper.
*   **HOW:** Pass the `logicalText` (a pointer to the text buffer), the `length` of the text, and the `baseDirection` (LTR or RTL). It returns a buffer of integer indices mapping the logical positions to visual positions.

```cpp
const char* text = "Hello أهلاً";
int length = 11;
Dali::TextAbstraction::BidirectionalSupport bidiSupport = Dali::TextAbstraction::BidirectionalSupport::Get();

// Create a vector to store the resulting visual indices
std::vector<int> visualIndices(length);

// Perform the reordering
bidiSupport.Reorder(text, length, Dali::TextAbstraction::DIRECTION_LEFT_TO_RIGHT, visualIndices.data());

// visualIndices now contains the visual mapping for the text buffer
```

## Querying Directional Properties

Identifying the directionality of specific text segments allows for dynamic UI adjustments, such as aligning text to the right when the content is predominantly RTL.

### GetParagraphDirection and GetCharactersDirection

These methods provide the metadata necessary to determine the layout context of your strings.

*   **WHAT:** These methods return the resolved directionality of either a full paragraph or individual characters within a string.
*   **WHY:** Use these to adjust component padding, alignment, or focus indicators based on the text detected at runtime.
*   **HOW:** Pass the relevant text buffer to retrieve a `CharacterDirection` constant. 

```cpp
Dali::TextAbstraction::BidirectionalSupport bidiSupport = Dali::TextAbstraction::BidirectionalSupport::Get();
const char* text = "أهلاً";

// Check the paragraph direction
Dali::TextAbstraction::CharacterDirection dir = bidiSupport.GetParagraphDirection(text, 6);

if (dir == Dali::TextAbstraction::DIRECTION_RIGHT_TO_LEFT) {
    // Apply RTL-specific UI layout adjustments
}
```

## Mirroring Characters and Glyphs

Certain characters, such as brackets `()` or less-than/greater-than signs `<>`, must be "mirrored" (flipped horizontally) when the text direction is RTL to ensure they point in the logically correct direction.

### GetMirroredText

The `GetMirroredText` method automates the transformation of characters that require a mirrored counterpart.

*   **WHAT:** This function scans a character buffer and replaces characters with their Unicode mirror equivalents where applicable.
*   **WHY:** Essential for maintaining the visual logic of mathematical formulas or bracketed phrases in RTL contexts.
*   **HOW:** Pass the source text buffer and a destination buffer. The method returns the number of characters processed.

```cpp
char text[] = "(Text)";
char mirroredText[6];
Dali::TextAbstraction::BidirectionalSupport bidiSupport = Dali::TextAbstraction::BidirectionalSupport::Get();

// Generate the mirrored version of the text
bidiSupport.GetMirroredText(text, 6, mirroredText);

// mirroredText is now ready for rendering in an RTL context
```

## Best Practices for Bidirectional Content

When working with bidirectional text, consistency and performance are key to a smooth user experience.

*   **Cache Results:** Text analysis is computationally intensive; cache the reordering results if the text content does not change frequently.
*   **Handle User Input:** Always treat user-inputted text as logical text before passing it to the `[BidirectionalSupport](./bidirectional-support.md)` module.
*   **Consistency:** Ensure that your UI layout (e.g., text alignment) matches the resolved `ParagraphDirection` retrieved from the support module.
*   **Alignment:** If your app supports both LTR and RTL [layouts](./layouts.md), consider using the resolved direction to flip the layout of containers containing [text](./text.md). → See: [TextLayoutModule] (Placeholder for parent layout documentation).

> Warning: Avoid performing intensive reordering operations inside the main render loop; perform these on a background thread or during the [text](./text.md) layout [update](./update.md) phase to maintain frame rate stability.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/bidirectional-support)
