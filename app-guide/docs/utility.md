---
id: utility
title: "utility"
sidebar_label: "utility"
---
## Introduction to NPatch Utilities

The `utility` module in DALi provides a specialized suite of tools for handling nine-patch (NPatch) imagery, which allows developers to create UI elements that scale gracefully without distorting corners or edge details. By utilizing these utilities, you move away from manual coordinate calculation for stretch regions, ensuring that buttons, panels, and containers remain visually consistent across diverse screen resolutions.

This module is distinct from general image processing libraries because it is deeply optimized for the DALi [rendering](./rendering.md) pipeline. You should use the `utility` suite whenever your application requires UI components that must expand or contract to fit dynamic [text](./text.md) or varying screen sizes while maintaining high-quality graphical integrity.

## Working with NPatchHelper

The `NPatchHelper` provides a high-level abstraction to automate the identification and application of stretchable regions. This class simplifies the process of binding image resources to UI controls by abstracting the raw pixel data into manageable scaling definitions.

### Automating Stretchable Regions
The `NPatchHelper` is designed to interpret nine-patch meta-data associated with image resources, allowing the framework to automatically determine which parts of an image should stretch and which should remain static.

> Note: `NPatchHelper` relies on the underlying image resource containing valid nine-patch metadata. If the asset is a standard image without stretch definitions, the helper will treat the entire asset as a static region.

```cpp
// Example: Configuring a UI component with NPatchHelper
#include <dali/dali.h>
#include <dali/public-api/images/resource-image.h>

using namespace Dali;

void SetupButton(Actor parent)
{
  // Initialize the helper with a resource path
  auto helper = NPatchHelper::New("button_background.9.png");
  
  // Apply the helper to a control or actor property
  ImageView button = ImageView::New();
  button.SetImage(helper.GetImage());
  
  parent.Add(button);
}
```

## Advanced NPatchUtility Operations

The `NPatchUtility` class provides low-level mathematical support for calculating scaling offsets and sub-region slicing. These operations are intended for developers building custom rendering components that require precise control over how internal image fragments are distributed.

### Performing Complex Calculations
When implementing custom resizing logic, you may need to calculate the remainder of the available space once static corners have been accounted for. `NPatchUtility` allows for the direct manipulation of scaling factors based on input geometry.

*   **WHAT:** Calculates the distribution of stretching regions given a target bounding box size and the original asset dimensions.
*   **WHY:** Use this when building custom layout containers that need to query the optimal stretch configuration before a frame is rendered.
*   **HOW:** Parameters involve the source image dimensions (integer width/height) and the target view dimensions. It returns an object representing the active slice rectangles.

```cpp
// Example: Using NPatchUtility for custom layout calculations
void CalculateCustomScaling(int targetWidth, int targetHeight)
{
    auto regions = NPatchUtility::CalculateRegions(targetWidth, targetHeight, 100, 100);
    
    // Use the resulting regions to update custom child actors
    // This allows for granular control over element placement
}
```

## Performance Optimization for Scalable Assets

Optimizing multi-resolution assets is critical for maintaining high frame rates. The `[utility](./utility.md)` module assists by providing mechanisms to cache calculated regions, preventing redundant re-calculations during layout passes.

> Warning: Frequent re-calculation of NPatch boundaries during every frame will negatively impact the UI thread. Cache your calculated slice regions whenever the target actor size remains constant.

*   **Caching Strategy:** Always store the result of `NPatchUtility` operations within your component class. Only re-invoke calculations when the size change exceeds a specific threshold or when the parent container undergoes a significant resize event.

## Common Integration Patterns

Integrating `NPatchHelper` and `NPatchUtility` ensures that your UI remains both performant and maintainable. The most effective pattern involves using the `Helper` for standard image loading and the `Utility` for specialized dynamic layout overrides.

### Creating a Resizable Panel
This example demonstrates a standard pattern for creating a background panel that respects nine-patch scaling while maintaining internal child padding.

```cpp
// Full Example: Dynamic Resizable Panel
class MyPanel : public CustomActor
{
public:
  MyPanel()
  {
    // 1. Utilize helper to set the base asset
    mHelper = NPatchHelper::New("panel_bg.9.png");
    mBackground = ImageView::New(mHelper.GetImage());
    
    // 2. Perform initial layout calculations using Utility
    UpdateLayout(200, 200);
    
    Add(mBackground);
  }

  void UpdateLayout(int w, int h)
  {
    mBackground.SetSize(w, h);
    // Use utility to align child content within the center stretch region
    auto padding = NPatchUtility::GetPadding(w, h);
    // Apply padding logic to child actors...
  }

private:
  NPatchHelper mHelper;
  ImageView mBackground;
};
```

→ See: `LayoutGroup` for more details on managing child element positioning relative to these dynamic background assets.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/utility)
