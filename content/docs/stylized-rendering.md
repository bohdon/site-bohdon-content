---
draft: true
title: Stylized Rendering
date: 2022-07-05T00:20:00-04:00
tags:
  - unreal
  - graphics
  - tech-art
imageIcon: fa-sharp fa-light fa-circle-half
summary: A detailed breakdown of non-photorealistic rendering methods used in Creature in the Well, Stonefly, and other projects.
---
This doc covers a wide array of topics related to the design of a non-photorealistic shading model and other rendering features. It's meant to cover conceptual thought process and key details of the technical implementation, rather than step by step break down of building it from scratch.

> Check out the [stylized rendering branch of my UnrealEngine fork](https://github.com/bohdon/UnrealEngine/tree/feature-stylized-rendering) for a full implementation.

The techniques below were used at different stages and in different ways on [Creature in the Well](/work/creature-in-the-well), [Stonefly](/work/stonefly), [Manifest 99](/work/manifest-99), and [Super Giant Robot Brothers](https://www.netflix.com/title/80211128). The ideas evolved and varied from project to project and each technique is meant to represent a possible way to approach specific goals, not a catch all solution for non-photorealistic rendering.


# Shading Model

A lot of tricks can be done in a material using one of the builtin shading models to accomplish non photorealistic art styles, but a shading model allows changing some key elements about how a surface responds to light. There are a few variations on the shading model that tailored it specific to the needs and restrictions of different projects, but the overall features included:

- Control over light 'edge'
- Control over specular masking, sharpening, and coloration
- Separate features that only appear under direct light
- Ability to blend between flattened and unflattened N dot L

## Light Edge

Perhaps the most significant and well known technique of this art style is simplifying the smooth gradients that come from object normals under light to flat colors and sharper edges. Those detailed features come from the N dot L or `NoL` which represents the dot product of the surface normal to the light direction. One of the more common ways to flatten that NoL is to use smoothstep, i.e. `smoothstep(NoL, 0.5, 0.5)`.

```glsl
float EdgePosition = GBuffer.CustomData.x;
float EdgeSharpness = 1 / GBuffer.CustomData.y;
float SpecularMask = GBuffer.CustomData.z;
float SpecRoughnessCutoff = GBuffer.CustomData.w;

// sharpen and offset NoL
float StylizedNoL = saturate((NoL - EdgePosition) * EdgeSharpness);

FDirectLighting Lighting;
Lighting.Diffuse = AreaLight.FalloffColor * (Falloff * StylizedNoL) * Diffuse_Lambert(GBuffer.DiffuseColor);
```

TODO: blend between flat and unflat

This shading code goes in `ShadingModels.ush`

## Stylized Specular Highlights

TODO: example ggx and considerations

## Separate Features In Light

One of the goals of this art style was to be able to represent certain features while under direct light, while hiding or abstracting away the features in shadows.

TODO

# Lighting Shaders

TODO: implementing shadow mask

# Material

TODO: all the techniques, various ways to use light edge, etc

# Lighting

TODO: falloff control, light cones, sharp shadows
