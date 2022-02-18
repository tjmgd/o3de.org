---
linkTitle: Chromatic Aberration
title: Chromatic Aberration Component
description: Open 3D Engine (O3DE) Chromatic Aberration component reference. 
toc: true
---

The **Chromatic Aberration** component creates *Chromatic Aberration*, a post-processing effect that simulates the failure of a lens to focus all colors to the same point.


## Provider

[Atom Gem](/docs/user-guide/gems/reference/rendering/atom/atom)


## Dependencies

[PostFX Layer component](./postfx-layer)



## Chromatic Aberration properties

| Property | Description | Value | Default |
| - | - | - | - |
| **Enable** | If enabled, activates the effect. | Boolean | `Disabled` |
| **Overrides - Enabled Override** | If enabled, all component properties will be set to the values specified in the **Overrides** property group. | Boolean | `Enabled` |
| **Strength** | Controls the magnitude of the color displacement.  | Float: 0.0 - 1.0 | `0.01` |
| **Blend** | Scales the blending of the effect with the original image, i.e. reduces sharpness. | Float: 0.0 - 1.0 | `0.5` |
