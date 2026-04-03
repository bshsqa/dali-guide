---
id: builder
title: "builder"
sidebar_label: "builder"
---
## Introduction to the DALi Builder

The DALi Builder module provides a declarative mechanism for constructing user interfaces by separating visual definitions from C++ logic. By utilizing external JSON-based files, developers can define hierarchical UI structures, property configurations, and resource associations, significantly reducing the boilerplate code required for scene graph construction.

You should use the Builder module when you want to improve maintainability, support theming, or enable faster iteration cycles by modifying UI [layouts](./layouts.md) without recompiling your C++ source code. It is distinct from manual [object](./object.md) instantiation because it automates the creation of property-set actors and complex tree hierarchies through a data-driven pipeline.

## Defining UI with JSON

UI definitions are authored in JSON, which acts as the blueprint for your scene graph. This structure allows you to declare actors, their properties, and their parent-child relationships in a human-readable format.

The JSON schema expects keys that map directly to DALi `Property` names and standard actor types. By defining these assets externally, you decouple the visual representation from the application logic, allowing for rapid interface prototyping.

## Parsing JSON Definitions

The `JsonParser` class is the entry point for converting raw JSON [text](./text.md) into a memory-resident `TreeNode` structure. It provides validation and error reporting to ensure your UI definitions are syntactically sound before they are applied to the scene.

### Using JsonParser
This class parses a character buffer into a tree structure. Use this to verify your JSON schema before passing it to the Builder for [object](./object.md) instantiation.

*   **WHAT:** Parses a string into a `TreeNode` [object](./object.md).
*   **WHY:** Necessary to validate your JSON file and generate the internal [object](./object.md) representation required by the Builder.
*   **HOW:** Pass a string to `Parse(const std::string& buffer)`. It returns a `TreeNode` handle. If parsing fails, the resulting node will indicate an error state.
*   **CODE:**
```cpp
#include <dali/dali.h>
#include <dali/devel-api/builder/json-parser.h>

std::string jsonString = "{\"type\":\"ImageView\", \"size\":[100, 100]}";
Dali::JsonParser parser = Dali::JsonParser::New();
parser.Parse(jsonString);
Dali::TreeNode root = parser.GetRoot();
```

## Navigating the UI Tree

Once the JSON is parsed into a `TreeNode` structure, you may need to inspect or modify the definitions programmatically before applying them. The `TreeNode` class provides a light-weight wrapper around the data, while `ConstIterator` allows for sequential access to child nodes.

### Traversing with TreeNode
The `TreeNode` provides methods to query values by key and iterate through child elements.

*   **WHAT:** Provides access to node values and children within the parsed tree.
*   **WHY:** Allows you to perform programmatic adjustments to your UI definition, such as injecting dynamic values into the tree before the final build.
*   **HOW:** Use `FindChildByName(const std::string& name)` to locate nodes or the `CBegin()`/`CEnd()` methods to traverse hierarchies.
*   **CODE:**
```cpp
Dali::TreeNode root = parser.GetRoot();
for (Dali::TreeNode::ConstIterator it = root.CBegin(); it != root.CEnd(); ++it)
{
  Dali::TreeNode child = *it;
  // Inspect child node properties
}
```

## Loading and Applying Builds

After parsing and potentially manipulating your `TreeNode`, the `Builder` class performs the actual heavy lifting: instantiating the DALi objects (Actors, Visuals, etc.) and attaching them to your scene.

### Utilizing the Builder
The `Builder` acts as the engine that converts `TreeNode` definitions into concrete DALi Actors.

*   **WHAT:** Takes a `TreeNode` and applies the definitions to the scene graph.
*   **WHY:** This is the primary mechanism to transform static data into a functional, interactive UI on the screen.
*   **HOW:** Create a `Builder` instance, use `AddFromString()` or `AddFromFile()` to load your definitions, and call `Create()` to generate the UI components.
*   **CODE:**
```cpp
Dali::Builder builder = Dali::Builder::New();
builder.AddFromString(jsonString);
Dali::BaseHandle rootActor = builder.Create("MainView");
Dali::Stage::GetCurrent().Add(static_cast<Dali::Actor>(rootActor));
```

> Note: Ensure all required resources (like [images](./images.md) or fonts) referenced in the JSON are available at runtime, otherwise the Builder will fail to resolve them during instantiation.

## Handling Dynamic UI Updates

Dynamic updates involve modifying the JSON tree or applying new property values at runtime to reflect changes in application state. While the Builder is primarily designed for construction, you can use it to refresh specific sub-trees or [update](./update.md) properties on existing actors defined by the build.

> Warning: Frequent destruction and recreation of UI trees via the Builder can cause performance spikes. Prefer updating existing properties on instantiated Actors for smooth animations and state transitions.

## Best Practices for Large-Scale UI

For complex applications, managing a single monolithic JSON file becomes impractical. To scale effectively, adopt a modular approach.

*   **Componentization:** Break your UI into small, reusable JSON snippets representing functional widgets.
*   **Resource Management:** Keep constant values, colors, and asset paths in a shared configuration file to maintain consistency across the application.
*   **Performance:** Pre-parse JSON files during application initialization rather than loading them in response to user input to avoid frame drops.
*   See: [ResourceImage] for details on handling [images](./images.md) referenced by your JSON definitions.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/builder)
