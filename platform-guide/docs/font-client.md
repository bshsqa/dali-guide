---
id: font-client
title: "FontClient"
sidebar_label: "FontClient"
---
## Overview of [FontClient](./font-client.md)

The `FontClient` serves as the primary interface within the DALi `text-abstraction` layer for font discovery, system-wide font metric management, and glyph-caching coordination. It acts as the bridge between the high-level toolkit [text](./text.md) [rendering](./rendering.md) systems and the low-level glyph rasterization engine.

Developers should utilize `FontClient` when they need to perform low-level typography tasks such as querying supported system fonts, managing DPI-aware scaling for custom [text](./text.md) [layouts](./layouts.md), or manually triggering cache invalidation cycles to reclaim memory. It is the central authority for system-wide font defaults and performance diagnostics related to [text](./text.md) [rendering](./rendering.md).

## Initialization and Lifecycle Management

`FontClient` follows a handle-based lifecycle [common](./common.md) to DALi components. It is typically managed as a singleton to ensure consistency across the application’s [text](./text.md) [rendering](./rendering.md) pipeline, though it can be instantiated specifically for custom [rendering](./rendering.md) contexts.

### Singleton and Handle Management

The `FontClient::Get()` method provides access to the shared global instance, while `FontClient::New()` allows for explicit instance creation if specific DPI overrides are required for a particular subsystem.

```cpp
#include <dali/devel-api/text-abstraction/font-client.h>

void InitializeFontServices()
{
    // Retrieve the shared singleton instance
    Dali::TextAbstraction::FontClient fontClient = Dali::TextAbstraction::FontClient::Get();
    
    // Alternatively, create a specific instance with custom DPI
    Dali::TextAbstraction::FontClient customClient = Dali::TextAbstraction::FontClient::New(160, 160);
}
```

> Note: Because `[FontClient](./font-client.md)` uses handle semantics, copying the object is inexpensive and safe, as it internally references the same underlying implementation.

## DPI and Font Scaling Configuration

Correct font scaling is critical for maintaining visual consistency across devices with varying pixel densities. `[FontClient](./font-client.md)` provides mechanisms to synchronize internal metrics with the windowing system or to force specific resolutions.

### DPI Configuration Methods

Use `SetDpiFromWindowSystem()` to automatically align the client with the host environment, or `SetDpi()` for manual override scenarios, such as rendering text to an off-screen buffer at a different resolution.

```cpp
void ConfigureDpi(Dali::TextAbstraction::FontClient& client)
{
    // Synchronize with the current display density
    client.SetDpiFromWindowSystem();
    
    // Or, set explicit DPI for custom rendering
    client.SetDpi(320, 320);
    
    unsigned int hDpi, vDpi;
    client.GetDpi(hDpi, vDpi);
}
```

## Cache Management and Memory Optimization

To maintain performance, the `[FontClient](./font-client.md)` maintains a glyph cache. In scenarios where the environment changes—such as system locale updates or large memory pressure events—you must manually flush these caches to prevent stale rendering data.

### Cache Invalidation

Use `ClearCache()` for general maintenance and `ClearCacheOnLocaleChanged()` specifically when the user updates the system language settings, which may impact glyph shaping and character mapping.

```cpp
void HandleSystemEvents(Dali::TextAbstraction::FontClient& client)
{
    // Clear cache when memory usage is critical
    client.ClearCache();
    
    // Clear cache upon system locale notification
    client.ClearCacheOnLocaleChanged();
}
```

## Atlas Configuration Constants

`[FontClient](./font-client.md)` defines the structural limits for texture atlasing. These constants are used to balance the trade-off between the number of draw calls (fewer, larger textures) and memory consumption (fewer, smaller textures).

### Atlas Constants Reference

The following constants define the boundaries for glyph texture atlases. Developers should use these when configuring custom atlas managers or debugging texture overflow issues.

*   `DEFAULT_TEXT_ATLAS_WIDTH` / `HEIGHT`: The base dimensions for an atlas block.
*   `MAX_TEXT_ATLAS_WIDTH` / `HEIGHT`: The hard upper limit imposed by the underlying GPU's texture size constraints.
*   `PADDING_TEXT_ATLAS_BLOCK`: The pixel padding required between glyphs to prevent sampling artifacts during texture filtering.

```cpp
void LogAtlasConstraints(Dali::TextAbstraction::FontClient& client)
{
    printf("Max Atlas Size: %dx%d\n", 
           Dali::TextAbstraction::FontClient::MAX_TEXT_ATLAS_WIDTH, 
           Dali::TextAbstraction::FontClient::MAX_TEXT_ATLAS_HEIGHT);
}
```

## Performance Monitoring and Diagnostics

For developers optimizing text-heavy applications, `[FontClient](./font-client.md)` provides built-in diagnostics to track the latency of glyph generation.

### Diagnostics API

Use `IsPerformanceLogEnabled()` to check the status of the debug system and `GetPerformanceLogThresholdTime()` to determine the sensitivity of the performance logging mechanism.

```cpp
void CheckPerformance(Dali::TextAbstraction::FontClient& client)
{
    if (client.IsPerformanceLogEnabled())
    {
        uint32_t threshold = client.GetPerformanceLogThresholdTime();
        printf("Performance logging is active. Threshold: %u ms\n", threshold);
    }
}
```

> Warning: Performance logging should be disabled in production releases as it can introduce additional CPU overhead during high-frequency glyph rasterization cycles.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/font-client)
