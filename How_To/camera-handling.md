---
layout: default
title: Camera Handling
parent: How-To
nav_order: 2
---

# Camera Handling
Camera implementation can vary wildly from game to game, but there is one important detail to remember: the camera should always follow the interpolated character transform; not just the raw Translation/Rotation component values. Following the non-interpolated character transform will often result in jitter.

The interpolated character transform values are found in the `LocalToWorld` component, and are calculated in the `TransformSystemGroup` For this reason, any character camera system should always update after the `TransformSystemGroup`