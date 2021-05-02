---
layout: default
title: Jumping and Landing
parent: How-To
nav_order: 7
---

# Jumping and Landing
Due to the character's grounding mecanisms, there are particularities to be aware of when it comes to jumping. Trying to make a character jump by simply giving it an upwards velocity will often fail because the character will still think it is grounded, and it'll try to snap right back to the ground on the following frame.

When trying to jump, it is recommended to call `KinematicCharacterBody.Unground()` along with the jump velocity change. This will make sure that the ground snapping mechanism won't snap your character back to ground. You can also use the pre-made `CharacterControlUtilities.StandardJump` function for a quick complete jump implementation.

In order to detect if your character has just landed or left ground, you can use `KinematicCharacterBody.HasBecomeGrounded` or `KinematicCharacterBody.HasBecomeUngrounded`.