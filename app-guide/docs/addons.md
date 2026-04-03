---
id: addons
title: "addons"
sidebar_label: "addons"
---
## Introduction to DALi AddOns

The DALi AddOns framework provides a modular architecture for extending application functionality beyond the core UI framework. It enables developers to integrate specialized features—such as enhanced media handling or layout extensions—dynamically at runtime, promoting a cleaner separation between core UI logic and specialized system features.

By utilizing the AddOns framework, developers can maintain a lightweight core application footprint, only loading heavy or specialized components when they are specifically required. This approach makes DALi applications more scalable and easier to maintain when dealing with diverse device configurations or optional feature sets.

## Loading and Initializing AddOns

To utilize AddOn functionality, your application must ensure the required modules are correctly registered within the DALi lifecycle. This process ensures that the requested features are available for instantiation when your application logic demands them.

> Note: Accessing AddOns requires that the underlying module is present on the host environment; attempting to load a non-existent AddOn will return an empty handle.

## Interacting with AddOn Interfaces

Once an AddOn is initialized, the application interacts with it through specific public-facing handles. Querying the registry allows you to retrieve these handles and invoke the specialized methods required to manipulate the specific feature set provided by the module.

## Managing AddOn Dependencies

Managing dependencies effectively ensures your application remains robust even when optional features are absent. By checking the validity of your AddOn handles before invocation, you can prevent runtime null-pointer exceptions and implement graceful degradation paths.

> Warning: Always verify that an AddOn handle is valid using a null check or equivalent logic before attempting to call its methods, as platform-level availability may vary.

## Best Practices for AddOn Usage

To ensure optimal performance, AddOns should be acquired and held only for the duration they are needed. Excessive polling or repeated retrieval of AddOn interfaces can introduce unnecessary overhead, particularly in high-frequency [rendering](./rendering.md) loops.

## AddOns Configuration and Scoping

AddOn behavior can often be modified through configuration parameters to ensure consistency across different UI views. Proper scoping of these configurations allows individual components to maintain specialized behavior without globally affecting the state of the entire application.

### Implementing a Specialized View via AddOn Logic

While the core DALi framework handles standard UI elements, specialized views often utilize AddOn interfaces to provide advanced [rendering](./rendering.md) or layout behaviors. For example, using the `AnimatedImageView` allows for the integration of complex image formats.

```cpp
#include <dali/dali.h>
#include <dali/ui/animated-image-view.h>

// Example: Configuring an AnimatedImageView to display a dynamic resource
void SetupAnimatedView(Dali::Ui::AnimatedImageView& imageView, const std::string& path)
{
  // Sets the URL for the animation resource
  imageView.SetResourceUrl(path);

  // Apply a color tint to the animation for visual consistency
  imageView.SetImageColor(Dali::Ui::UiColor(1.0f, 0.5f, 0.0f, 1.0f));
}

// Usage in an application context
void CreateView()
{
  Dali::Ui::AnimatedImageView myView = Dali::Ui::AnimatedImageView();
  SetupAnimatedView(myView, "resource://path/animation.gif");
  
  // The view is now ready to be added to the scene graph
}
```

### Absolute Layout Positioning

When custom positioning is required for specific components, the `[AbsoluteLayout](./absolute-layout.md)` and `AbsoluteLayoutParams` classes provide the necessary interface to control child placement precisely.

```cpp
#include <dali/ui/absolute-layout.h>

// Configuring layout parameters for a child view
void ConfigureChild(Dali::Ui::AbsoluteLayoutParams& params)
{
  // Position the child at (10.0, 50.0) with specific dimensions
  params.SetX(10.0f);
  params.SetY(50.0f);
  params.SetWidth(100.0f);
  params.SetHeight(200.0f);
}

// Creating and initializing an AbsoluteLayout container
void InitializeContainer()
{
  Dali::Ui::AbsoluteLayout layout = Dali::Ui::AbsoluteLayout::New();
  Dali::Ui::AbsoluteLayoutParams params = Dali::Ui::AbsoluteLayoutParams::New();
  
  ConfigureChild(params);
  
  // Use 'params' when adding children to the AbsoluteLayout container
}
```

> Note: `AbsoluteLayoutParams` serves as a container for per-child layout metadata; modifications to these parameters only affect the specific child to which they are applied. → See: [[Layout](./layout.md) Systems Guide]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/addons)
