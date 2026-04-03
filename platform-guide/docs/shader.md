---
id: shader
title: "Shader"
sidebar_label: "Shader"
---
## Introduction to the DALi [Shader](./shader.md) Subsystem

The `Shader` module in DALi provides the low-level interface for defining and managing GLSL programs that dictate the visual appearance of rendered objects. Unlike high-level material systems, the `Shader` module gives developers direct control over vertex and fragment pipelines, allowing for sophisticated custom effects, advanced lighting models, and non-photorealistic [rendering](./rendering.md) techniques.

You should use this module when standard `Renderer` materials are insufficient for your visual requirements and you need to supply custom GLSL code to the graphics hardware. It is distinct because it operates at the compilation boundary of the DALi [rendering](./rendering.md) pipeline, enabling pre-optimization of shader programs before they are submitted to the GPU. 

→ See: [[Renderer](./renderer.md)]

## [Shader](./shader.md) Compilation Architecture

The shader compilation architecture in DALi is designed to bridge the gap between human-readable GLSL source code and hardware-optimized GPU programs. It handles the parsing, validation, and binary generation stages, ensuring that shader errors are caught early in the development lifecycle.

The process begins when `RawShaderData` structures are processed by the internal pre-compiler. This subsystem validates syntax, resolves prefixes, and prepares the program objects to be uploaded to the GPU via the graphics abstraction layer. By managing this lifecycle centrally, DALi ensures that shader state is correctly synchronized between the main application thread and the background render thread.

## Defining Raw [Shader](./shader.md) Data

`Dali::ShaderPreCompiler::RawShaderData` is the primary data structure used to bundle shader source components and metadata for the compilation pipeline. Developers populate this struct to define the characteristics of their custom shaders before handing them off to the engine.

The structure fields include:
- `shaderCount`: The number of shaders described in the current data batch.
- `vertexPrefix`: Optional string prepended to the vertex shader (e.g., `#define` statements).
- `fragmentPrefix`: Optional string prepended to the fragment shader.
- `shaderName`: A unique identifier for the shader program used for debugging and caching.
- `vertexShader`: The main source code for the vertex shader.
- `fragmentShader`: The main source code for the fragment shader.
- `custom`: A placeholder for user-defined metadata or additional parameters required by the specific [rendering](./rendering.md) path.

```cpp
#include <dali/public-api/dali-adaptor.h>

void ConfigureMyShader() {
  Dali::ShaderPreCompiler::RawShaderData data;
  data.shaderCount = 1;
  data.shaderName = "MyCustomShader";
  data.vertexShader = "attribute mediump vec2 aPosition; ...";
  data.fragmentShader = "precision mediump float; ...";
  data.vertexPrefix = "#define USE_HIGH_PRECISION";
  data.fragmentPrefix = "";
  data.custom = "v1.0";
}
```

> Note: Ensure that `vertexShader` and `fragmentShader` strings follow the GLSL ES specifications supported by the target platform, as invalid syntax will cause the shader compilation to fail silently at runtime.

## Shader Pre-Compilation Patterns

Pre-compilation is a critical optimization technique used to eliminate frame-rate stuttering that occurs when shaders are compiled on-demand during scene transitions. By populating the `RawShaderData` struct and triggering the pre-compiler during the application's initialization phase, you ensure that the GPU binary is ready before the object appears on screen.

For complex applications, gather all necessary shader sources during the loading screen. By defining the `shaderName` accurately, the engine can potentially implement disk-based caching of the compiled binaries, drastically reducing subsequent launch times.

## Integration and Threading Model

The `[Shader](./shader.md)` subsystem is inherently cross-threaded to maintain high performance in the UI thread. While you define `RawShaderData` on the main application thread, the actual compilation and binary linkage occur on the background render thread.

Developers must ensure that the memory passed into the `RawShaderData` (specifically the strings for shader code) remains valid until the compilation process is acknowledged by the engine. Avoid modifying these strings after they have been submitted to the `ShaderPreCompiler`.

> Warning: Accessing the render thread's internal shader state is unsafe. Always interact with the `[Shader](./shader.md)` module through the provided public APIs to ensure thread synchronization is handled by the DALi kernel.

## Custom Shader Data Handling

The `custom` field within `RawShaderData` serves as an extension point for developers to attach implementation-specific metadata to their shader definitions. This is particularly useful for systems that require custom serialization or for identifying unique shader variants that share the same GLSL code but require different internal engine handling.

```cpp
void RegisterExtendedShader() {
  Dali::ShaderPreCompiler::RawShaderData data;
  // Initialize standard fields...
  
  // Use the custom field to pass a specialized configuration flag
  data.custom = "ENABLE_SHADOW_MAP_SUPPORT";
  
  // Submit to the rendering subsystem
}
```

By utilizing the `custom` field, you can create a mapping system where specific `RawShaderData` instances are retrieved based on application logic, effectively creating a factory pattern for your custom [rendering](./rendering.md) effects.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/shader)
