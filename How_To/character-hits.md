---
layout: default
title: Character Hits
parent: How-To
nav_order: 15
---

# Character Hits

The character detects various hits during its update. These hits can be a result of ground detection, movement sweeps, or overlap resolution.

In order to access those hits, every character has a `DynamicBuffer<KinematicCharacterHit>` buffer containing all hits detected for this frame. Similarly, every character also has a `DynamicBuffer<StatefulKinematicCharacterHit>` for stateful hits (hits with an "Enter/Exit/Stay" state). However, those hits will only be processed if `KinematicCharacterBody.ProcessStatefulCharacterHits` is enabled.

The moment where all hits for the frame have been detected is after `KinematicCharacterUtilities.MovementAndDecollisionsUpdate`. So you can iterate the hits in the buffer after this step is completed. After the `KinematicCharacterUpdateGroup` would also be a safe time to iterate on hits.