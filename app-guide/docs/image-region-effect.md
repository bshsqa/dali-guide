---
id: image-region-effect
title: "Image Region Effect"
sidebar_label: "Image Region Effect"
---
## Getting Started with Image Region Effects

The `ImageRegionEffect` allows developers to apply shader-based visual modifications to specific rectangular areas of an image or component texture. Unlike global effects that process an entire actor, this effect provides granular control by allowing you to define a sub-rectangle of the source material to manipulate, making it ideal for spotlighting, local distortion, or selective color grading.

You should use the `ImageRegionEffect` when your design requires a localized visual change within an image boundary without splitting the original asset into multiple files. It distinguishes itself from other [shader-effects](./shader-effects.md) by providing dedicated properties to map coordinate spaces directly to the texture UVs.

→ See: [ColorAdjustEffect], [BlurEffect]

## Configuring Region Coordinates

Defining the region is the primary step in using the `ImageRegionEffect`. The effect uses normalized coordinates (ranging from 0.0 to 1.0), where the defined rectangle determines which portion of the image texture the shader logic will be applied to.

### Setting the Region Property
The region is defined by a `Vector4` representing `(x, y, width, height)`. These values are relative to the original image dimensions, ensuring that the effect scales appropriately if the underlying actor size changes.

```cpp
// Example: Applying an effect to the center 50% of the image
auto effect = ImageRegionEffect::New();
Vector4 region(0.25f, 0.25f, 0.5f, 0.5f); // x, y, width, height
effect.SetProperty(ImageRegionEffect::Property::REGION, region);

ImageView imageView = ImageView::New("my_image.png");
imageView.SetImageEffect(effect);
```

> Note: If the region coordinates fall outside the 0.0 to 1.0 range, the behavior is clamped to the edge pixels of the texture.

## Adjusting Effect Parameters

Once a region is defined, you can control the intensity or specific visual traits of the effect applied within that boundary. These parameters allow you to fine-tune how the shader interacts with the selected pixels.

### Modifying Effect Intensity
Most region effects include an `INTENSITY` property to transition the effect from a subtle blend to a full-strength application.

```cpp
// Set the intensity of the effect to 75%
effect.SetProperty(ImageRegionEffect::Property::INTENSITY, 0.75f);
```

## Dynamic Updates and Animation

The `ImageRegionEffect` supports DALi’s property animation system. By animating the `REGION` or `INTENSITY` properties, you can create smooth visual transitions like a moving spotlight or a gradual focal blur.

### Animating the Effect Region
To move the affected area across an image, use the `Animation` class to interpolate the `Vector4` region property over time.

```cpp
Animation animation = Animation::New(2.0f);
animation.AnimateTo(Property(effect, ImageRegionEffect::Property::REGION), 
                    Vector4(0.5f, 0.5f, 0.2f, 0.2f));
animation.Play();
```

## Handling Image Constraints and Performance

Applying effects to large textures or multiple regions can impact rendering performance. It is important to match the aspect ratio of the `REGION` property with the aspect ratio of the source image to prevent texture stretching or unexpected artifacts.

> Warning: Avoid animating the `REGION` property on every frame if the target area is large, as this triggers constant re-calculation of the shader UV mapping which may cause frame drops on low-end hardware.

## Common Use Cases and Patterns

The following pattern demonstrates a "magnifying glass" effect, where the region is small and centered, and the intensity is set to highlight the focal area.

### Spotlight Focus Pattern
This pattern focuses the viewer's attention on a specific area by applying a distinct shader effect within a confined region while leaving the rest of the image in a neutral state.

```cpp
// Configure an image with a spotlight region
auto spotlightEffect = ImageRegionEffect::New();
spotlightEffect.SetProperty(ImageRegionEffect::Property::REGION, Vector4(0.4f, 0.4f, 0.2f, 0.2f));
spotlightEffect.SetProperty(ImageRegionEffect::Property::INTENSITY, 1.0f);

ImageView display = ImageView::New("scenery.jpg");
display.SetSize(400, 400);
display.SetImageEffect(spotlightEffect);
Stage::GetCurrent().Add(display);
```

By combining these properties, you can effectively manage selective visual processing within your DALi application, ensuring high-quality, performant graphics.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/image-region-effect)
