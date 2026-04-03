---
id: font-file-manager
title: "FontFileManager"
sidebar_label: "FontFileManager"
---
## Introduction to [FontFileManager](./font-file-manager.md)

The `FontFileManager` is the centralized authority within the DALi `text-abstraction` layer responsible for managing the lifecycle of font file resources. It provides a specialized mechanism to read, store, and retrieve font data, allowing the engine to minimize costly filesystem I/O by caching font buffers in memory for subsequent FreeType face creation.

You should use `FontFileManager` when you are implementing custom [text](./text.md) [rendering](./rendering.md) pipelines or platform-specific font loaders that need to bypass redundant disk access. It is distinct from other abstraction components because it operates at the buffer level, holding the raw data (`std::vector<uint8_t>`) required by the underlying font rasterizers, rather than simply managing font metadata or description properties.

→ See: [[FontClient](./font-client.md)]

## Lifecycle and Instance Management

The `FontFileManager` follows a singleton-like pattern to ensure that font cache state remains consistent across the [text](./text.md) [rendering](./rendering.md) subsystem. Instances are managed via the `Get()` static method, which returns a smart handle to the internal engine implementation.

### Retrieving the Instance
The `Get()` method is the primary entry point for accessing the manager. It returns a `FontFileManager` [object](./object.md), which acts as a lightweight handle to the underlying engine implementation.

```cpp
#include <dali/devel-api/text-abstraction/font-file-manager.h>

using namespace Dali::TextAbstraction;

// Obtain the global FontFileManager handle
FontFileManager fontManager = FontFileManager::Get();

// The object uses smart pointer semantics; it can be copied or moved
FontFileManager managerCopy = fontManager;
```

> Note: While `[FontFileManager](./font-file-manager.md)` uses smart pointer semantics, the internal implementation is thread-safe for standard operations. However, avoid heavy concurrent calls to `ClearCache()` while simultaneously performing lookups to prevent cache-miss latency spikes.

## Font Resolution and Discovery

The `[FontFileManager](./font-file-manager.md)` provides methods to probe whether a font file has already been loaded into memory. By utilizing `FindFontFile`, developers can determine if the required resource is ready for the FreeType face initialization without triggering a new disk read.

### Checking and Retrieving Cache Data
The `FindFontFile` overload with `Dali::Any` and `std::streampos` allows you to extract the cached binary data and its associated file size.

* **Parameters:**
  * `fontPath`: The `FontPath` object identifying the system font file.
  * `fontFilePtr`: A `Dali::Any` reference that will be populated with the cached binary buffer if found.
  * `fileSize`: A `std::streampos` reference that will be populated with the total size of the cached file.
* **Return:** Returns `true` if the font file exists in the cache, `false` otherwise.

```cpp
#include <dali/devel-api/text-abstraction/font-file-manager.h>

void AccessFont(const FontPath& path)
{
  auto manager = FontFileManager::Get();
  Dali::Any fontBuffer;
  std::streampos size;

  if (manager.FindFontFile(path, fontBuffer, size))
  {
    // Font is ready in memory; proceed with FreeType memory face creation
  }
}
```

## Caching Strategy and Memory Management

Effective memory management is critical when handling high-density font assets. The `[FontFileManager](./font-file-manager.md)` allows you to manually inject font data into the cache and purge the cache when memory pressure is detected.

### Caching and Clearing
Use `CacheFontFile` to store a font buffer after an initial disk load. If the memory footprint of your application grows significantly, use `ClearCache()` to release all stored font binary data.

* **CacheFontFile Parameters:**
  * `fontPath`: The path identifier for the font.
  * `fontFileBuffer`: An rvalue reference to a `Dali::Vector<uint8_t>` containing the raw font data.
  * `fileSize`: The exact stream position representing the file size.

```cpp
#include <dali/devel-api/text-abstraction/font-file-manager.h>

void StoreFont(const FontPath& path, Dali::Vector<uint8_t>&& data, std::streampos size)
{
  auto manager = FontFileManager::Get();
  manager.CacheFontFile(path, std::move(data), size);
}

void FlushResources()
{
  FontFileManager::Get().ClearCache();
}
```

> Warning: `ClearCache()` is a destructive operation. All currently cached buffers will be deallocated immediately. Ensure no active text rendering operations are currently referencing these pointers before calling this method.

## Integration with Text Rendering Pipeline

The platform integration layer relies on `[FontFileManager](./font-file-manager.md)` to bridge the gap between abstract font requests (e.g., "Regular Arial") and physical file paths. By pre-caching these files, the engine prevents the rendering thread from stalling due to disk latency during layout and text shaping operations.

When the text engine requires a new typeface, it first queries the `[FontFileManager](./font-file-manager.md)`. If the file is not found, the integration layer performs the disk I/O, caches the resulting buffer, and then proceeds to create the `FT_Face`. This pattern ensures that each unique font file is loaded into heap memory only once for the lifetime of the cache.

## Best Practices for Cache Optimization

To minimize file I/O overhead and ensure optimal performance, follow these guidelines:

1. **Warm the Cache:** If your application knows the set of required fonts (e.g., at application startup or scene load), perform a speculative `FindFontFile` check to trigger any necessary loads early.
2. **Handle Large Fonts:** Large CJK font files can consume significant memory. Be strategic with `ClearCache()`—call it when navigating to sections of the application that do not require specific large-scale typography.
3. **Use Move Semantics:** Always pass your `Dali::Vector<uint8_t>` using move semantics (`std::move`) when calling `CacheFontFile` to avoid deep copying large font binaries.

```cpp
// Example: Optimizing a font load
void EnsureFontIsLoaded(const FontPath& path)
{
  auto manager = FontFileManager::Get();
  if (!manager.FindFontFile(path))
  {
    // Load from disk manually
    Dali::Vector<uint8_t> buffer = LoadFileToVector(path);
    std::streampos size = buffer.Size();
    
    // Inject into cache
    manager.CacheFontFile(path, std::move(buffer), size);
  }
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/font-file-manager)
