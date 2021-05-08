---
layout: default
title: Utilities
parent: Core
nav_order: 4
---

# KinematicCharacterUtilities

This class contains all of the core functions for the creation & update of the character controller. Namely:
- `CreateCharacter`: Adds all the required character components to an entity
- `HandleConversionForCharacter`: handles the conversion from GameObject to Entity for a character
- `InitializationUpdate`: clears some variables and buffers at the start of the update.
- `ParentMovementUpdate`: handles moving the character based on its currently-assigned `KinematicCharacterBody.ParentEntity`, if any.
- `GroundingUpdate`: handles detecting character grounding.
- `MovementAndDecollisionsUpdate`: handles moving the character and solving collisions, based on `KinematicCharacterBody.RelativeVelocity`, rotation, character grounding, and various other properties in `KinematicCharacterBody`.
- `ParentMomentumUpdate`: handles preserving velocity momentum when getting unparented from a parent body (such as a moving platform).
- `ProcessStatefulCharacterHits`: handles filling the `StatefulKinematicCharacterHit` buffer on the character entity, with character hits that have an Enter/Exit/Stay state associated to them
- Various "CastCollider", "CalculateDistance", and "Raycast" utilities that only detect hits that the character would truly collide with (uses the `CanCollideWithHit` callback of the character processor)
- Various "Filter" methods that are used to filter out any hit that the character wouldn't truly collide with (uses the `CanCollideWithHit` callback of the character processor)
- The `DefaultMethods` subclass: provides default implementations for various character processor callbacks, as well as default implementations for detecting moving platforms, pushing ground, and detecting steps


# CharacterControlUtilities

This class contains helper functions to allow you to control a character's velocity and rotation. It also contains a default character jump implementation. The default `MyCharacterProcessor.HandleCharacterControl` makes use of several of these functions.


# PhysicsUtilities

This class contains various physics helper functions.


# MathUtilities

This class contains various math helper functions.