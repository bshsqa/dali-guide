---
id: font-file-manager
title: "FontFileManager"
sidebar_label: "FontFileManager"
---
## Introduction to Font File Manager

The `FontFileManager` is a specialized [utility](./utility.md) within the DALi [text-abstraction](./text-abstraction.md) module designed to provide fine-grained control over font resource resolution. Unlike standard [text](./text.md)-[rendering](./rendering.md) components that automatically resolve system fonts, the `FontFileManager` allows developers to explicitly locate, map, and cache specific font files from the file system, making it essential for applications requiring custom typography, dynamic font swapping, or assets stored in non-standard application directories.

Use this manager when your application needs to handle font loading programmatically or when you must ensure that specific font assets are prepared in memory before they are referenced by [text](./text.md)-shaping engines. It provides a distinct advantage in performance-sensitive scenarios by allowing for manual cache pre-warming, effectively reducing I/O latency during UI transitions.

→ See: [TextAbstraction](https://dali.github.io/text-abstraction-guide)

## Getting the Manager Instance

The `FontFileManager` is implemented as a singleton to ensure a centralized, consistent registry of font assets across the application lifecycle. Because font resolution is a global resource-heavy operation, retrieving the instance from the abstraction layer ensures thread-safe access to the underlying font cache.

### Get()
Retrieves the global singleton instance of the `FontFileManager`.

*   **What:** Provides a reference to the `FontFileManager` [object](./object.md).
*   **Why:** You must obtain this instance to access the methods required for locating or caching font files.
*   **How:** This method requires no parameters and returns a reference to the `FontFileManager` singleton.
*   **Code:**
```cpp
#include <dali/devel-api/text-abstraction/font-file-manager.h>

void InitializeFontManagement() 
{
  Dali::TextAbstraction::FontFileManager& fontManager = 
    Dali::TextAbstraction::FontFileManager::Get();
  
  // Proceed to use the manager instance
}
```

## Locating Font Files

Finding the absolute path of a font file based on its family name and style is a frequent requirement when localizing an application or applying brand-specific typefaces. The manager abstracts the platform-specific search logic, allowing you to query the registry for valid font file paths.

### FindFontFile()
Resolves a file system path based on the requested font family and font style.

*   **What:** Searches for a font that matches the provided criteria and returns the path to the font file.
*   **Why:** Use this to verify the existence of a custom font before attempting to load it or to dynamically fetch the file path for custom font-rendering logic.
*   **How:** 
    *   `fontFamily` (const std::string&): The name of the font family (e.g., "SamsungOne").
    *   `fontStyle` (const FontStyle::Style&): The stylistic variant (e.g., Bold, Italic).
    *   Returns a `std::string` containing the absolute path to the font file. If no matching font is found, an empty string is returned.

*   **Code:**
```cpp
#include <dali/devel-api/text-abstraction/font-file-manager.h>

std::string ResolveCustomFont()
{
  auto& manager = Dali::TextAbstraction::FontFileManager::Get();
  
  std::string family = "SamsungOne";
  Dali::TextAbstraction::FontStyle::Style style = Dali::TextAbstraction::FontStyle::BOLD;
  
  std::string path = manager.FindFontFile(family, style);
  
  if(!path.empty()) 
  {
    return path;
  }
  return "default_font.ttf";
}
```

## Managing the Font Cache

Managing the memory footprint of loaded fonts is critical for high-performance applications. The `[FontFileManager](./font-file-manager.md)` provides methods to manually influence the cache, ensuring that high-priority fonts are kept in memory while unused assets can be purged.

### CacheFontFile() and ClearCache()
These methods allow for proactive management of the internal font resource cache.

*   **What:** `CacheFontFile` loads a font into the manager's memory, while `ClearCache` removes all currently cached font information.
*   **Why:** Use `CacheFontFile` to pre-load a heavy font asset during an application's splash screen to prevent UI stutters. Use `ClearCache` when switching application modules to free up memory.
*   **How:**
    *   `CacheFontFile(const std::string& path)`: Accepts the absolute path to the font file.
    *   `ClearCache()`: No parameters.
*   **Code:**
```cpp
void OptimizeFonts()
{
  auto& manager = Dali::TextAbstraction::FontFileManager::Get();
  
  // Pre-load a primary branding font
  manager.CacheFontFile("/resources/fonts/brand_font.ttf");
  
  // Clear cache if memory pressure is detected or changing states
  // manager.ClearCache();
}
```

> **Note:** The `ClearCache()` method should be used cautiously; frequent clearing and re-caching will induce significant disk I/O and performance degradation if the application requests these fonts immediately after.

## Best Practices for Font Resource Loading

To ensure your application remains performant and avoids unnecessary I/O blocking, adhere to the following patterns when interacting with the `[FontFileManager](./font-file-manager.md)`:

1.  **Resolve at Startup:** Resolve paths for your core UI fonts once during initialization and store these paths in a local variable or struct. Avoid calling `FindFontFile` inside the `OnUpdate` or `OnRender` cycle, as this may lead to frame drops.
2.  **Use Caching Sparingly:** Only use `CacheFontFile` for fonts that are guaranteed to be used in the current scene. Do not attempt to cache every font installed on the system, as this will lead to high memory overhead.
3.  **Handle Missing Files:** Always check if the string returned by `FindFontFile` is empty. Never assume a font file exists just because it is defined in your style requirements.
4.  **Platform-Level Detail:** If you require custom font-loading behaviors that involve complex font-family fallback chains or specific memory-mapped font buffers, this falls under platform-level detail. Refer to the *DALi Platform Integration Guide* for extending [text-abstraction](./text-abstraction.md) services.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/font-file-manager)
