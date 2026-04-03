---
id: shader
title: "Shader"
sidebar_label: "Shader"
---
## Introduction to [Shader](./shader.md) Usage

The `Shader` class is the primary mechanism in DALi for injecting custom programmable pipeline logic into the [rendering](./rendering.md) process. While DALi provides highly optimized built-in shaders for standard UI components, the `Shader` class is used when you need to implement custom visual effects, such as unique image filters, complex geometry deformations, or procedurally generated textures that are not supported by standard primitives.

Use `Shader` when your application requires low-level control over vertex transformation or fragment shading to achieve a specific artistic or performance-driven visual style. It is distinct from standard [rendering](./rendering.md) components because it acts as a low-level bridge between your GLSL code and the DALi [rendering](./rendering.md) engine, allowing you to define custom uniform variables and attribute mappings that interact directly with your UI controls.

## Preparing Raw [Shader](./shader.md) Data

To create a `Shader` [object](./object.md), you must define the source code for the vertex and fragment stages and wrap them in a data structure that the DALi engine can process. This preparation ensures that your shader code is validated and ready for the GPU pipeline before runtime initialization.

### Utilizing RawShaderData

The `RawShaderData` structure is a container for holding the vertex and fragment shader source strings. It serves as the input specification for the `Shader::New` factory method, ensuring the engine knows the exact GLSL code to compile.

*   **WHAT:** This structure holds the essential GLSL source code, naming conventions, and metadata for a shader.
*   **WHY:** By encapsulating the strings in this structure, you provide the engine with a clean, unified package that facilitates efficient compilation and caching.
*   **HOW:**
    *   `vertexShader`: A `std::string` containing the GLSL vertex shader code.
    *   `fragmentShader`: A `std::string` containing the GLSL fragment shader code.
*   **CODE:**

```cpp
#include <dali/public-api/rendering/shader.h>

std::string vertexSource = "attribute mediump vec2 aPosition; ... void main() { ... }";
std::string fragmentSource = "precision mediump float; ... void main() { ... }";

Dali::Shader shader = Dali::Shader::New(vertexSource, fragmentSource);
```

> Note: Ensure your GLSL code conforms to the version required by the platform; consult the platform guide for specific GLSL version support.

## Configuring Shader Prefixes and Names

Managing shader variants and debugging is simplified by assigning names and prefixes. These identifiers help you track specific shader instances within your application's rendering tree.

### Using Shader Names

The `[Shader](./shader.md)` class includes properties that allow you to assign a unique string identifier, which is useful for debugging and internal resource management.

*   **WHAT:** The `shaderName` property allows you to assign a readable string to a shader instance.
*   **WHY:** This is critical when debugging rendering issues, as it allows you to identify which specific shader is being executed for a given visual element in the frame debugger.
*   **HOW:** Pass the name string during or after construction.
*   **CODE:**

```cpp
Dali::Shader myShader = Dali::Shader::New(vertexSource, fragmentSource);
myShader.SetProperty(Dali::Shader::Property::SHADER_NAME, "MyCustomBlurShader");
```

## Working with Custom Shader Attributes

Shaders often require per-vertex data that differs from standard position and texture coordinate data. You can map custom attributes from your buffers directly to the shader to enable advanced rendering techniques.

### Defining Uniforms and Attributes

While `[Shader](./shader.md)` handles the source code, communication between the CPU and the GPU happens via uniforms. These are values defined in your GLSL code that you can update dynamically from your DALi code.

*   **WHAT:** Uniforms allow the application to push data like float, vector, or matrix values to the GPU every frame or whenever a property changes.
*   **WHY:** This enables dynamic effects, such as changing the color of an object or animating a transition, without needing to recompile the shader.
*   **HOW:** Once the `[Shader](./shader.md)` is applied to a `[Renderer](./renderer.md)`, you update the values via the `[Renderer](./renderer.md)` properties (→ See: [Renderer]).

```cpp
// Within the shader GLSL
// uniform mediump float uTime;

// In DALi C++
renderer.SetProperty(Renderer::Property::SHADER, myShader);
renderer.RegisterProperty("uTime", 0.0f); // Map uniform to a renderer property
```

## Optimizing Shader Compilation

Compiling shaders can be a computationally expensive operation that may introduce stuttering if done synchronously during an animation or transition.

### Pre-compilation Strategies

DALi provides mechanisms to ensure that shaders are compiled during application startup or idle periods, rather than during the first render call.

*   **WHAT:** Compilation caching ensures that the GPU driver does not re-compile the same shader source code multiple times.
*   **WHY:** This minimizes frame drops and reduces the time required for a scene to appear on screen.
*   **CODE:** Keep your shader source strings as `static const` or `constexpr` variables to facilitate efficient reuse and identification by the engine's internal cache.

## Troubleshooting Shader Errors

Shader development often involves iterative debugging of GLSL code. Errors in syntax or semantic usage can prevent the shader from linking, resulting in a black surface or complete rendering failure.

### Handling Compilation Failures

If a `[Shader](./shader.md)` fails to compile, the DALi engine will typically provide an error log via the debug output.

*   **Best Practices:**
    1.  **Check Precision Qualifiers:** Always specify `mediump` or `highp` for all variables; missing these is a [common](./common.md) cause of compilation failure.
    2.  **Verify Variable Matching:** Ensure the names of uniforms and attributes in your GLSL code exactly match the strings used in your C++ code.
    3.  **Use Simple Shaders First:** Start with a "passthrough" shader (a shader that simply draws the input) and incrementally add your custom logic to isolate syntax errors.
    4.  **Platform-Level Detail:** Advanced shader debugging tools are considered platform-level details; consult the platform guide for information on external GPU debugging tools.

> Warning: Always check the application log output when a new shader is added; the GLSL compiler logs will detail the specific line and character of any syntax errors.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/shader)
