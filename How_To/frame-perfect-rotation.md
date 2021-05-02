---
layout: default
title: Frame-Perfect Rotation
parent: How-To
nav_order: 14
---

# Frame-Perfect Rotation
The default code-generated character system, which updates at a fixed timestep, may not always be the ideal place to handle character rotation. This is especially true when you have a camera that rotates at a variable timestep, and you need the character to follow the rotation of the camera 1:1.

In those cases, it is recommended to handle character rotation in a separate system that updates at a variable timestep (in the `SimulationSystemGroup`, for example). This will give you perfectly smooth and responsibe rotations. The character from the OnlineFPS sample uses this approach, in `OnlineFPSCharacterVariableUpdateSystem`.