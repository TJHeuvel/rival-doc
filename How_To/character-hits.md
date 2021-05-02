---
layout: default
title: Character Hits
parent: How-To
nav_order: 15
---

# Character Hits

The character detects various hits during its update. These hits can be a result of ground detection, movement sweeps, or overlap resolution.

In order to access those hits, every character has a `DynamicBuffer<KinematicCharacterHit>` buffer containing all hits detected for this frame. The moment where all hits for the frame have been detected is after `KinematicCharacterUtilities.MovementAndDecollisionsUpdate`. So you can iterate the hits in the buffer after this step is completed.