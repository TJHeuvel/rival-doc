---
layout: default
title: Parameters
parent: Fundamentals
nav_order: 3
---

# Character Controller Parameters

The core parameters of the character controllers are found in the "AuthoringKinematicCharacterBody" struct that comes with the generated authoring component for your character. Here is an overview of these parameters:

____________________________________________________________________________________

## General Properties

### CustomPhysicsBodyTags
Physics tags to be added to the character's physics body

### UseInterpolation
Enables interpolating the character's transform between fixed update steps, for smoother movement

____________________________________________________________________________________

## Grounding

### DetectGrounding
Enables detecting and storing ground hits

### EvaluateGrounding
Enables all logic related to constraining the character movement to the detected ground surface

### MinGroundProbingDistance
Minimum distance used to detect grounding

### MaxGroundedSlopeAngle
The max slope angle that the character can be considered grounded on

### SnapToGround
Enables snapping to the ground surface below the character

### GroundSnapDistance
The distance that the character will try to snap to the ground surface below it

____________________________________________________________________________________

## Collisions

### DetectMovementCollisions
Enables detecting and solving movement collisions with a collider cast, based on character's velocity

### DecollideFromOverlaps
Enables detecting and solving overlaps

### AddCloseHitsForVelocityProjection
Enables doing an extra physics check to project velocity on initial overlaps before the character moves. This can help with tunneling issues with non-circular character colliders, but has a performance cost.

### MaxContinuousCollisionsIterations
The maximum amount of times per frame that the character should try to cast its collider for detecting hits

### MaxOverlapDecollisionIterations
The maximum amount of times per frame that the character should try to decollide itself from overlaps

### DiscardMovementWhenExceedMaxIterations
Whether we should reset the remaining move distance to zero when the character exceeds the maximum collision iterations

### KillVelocityWhenExceedMaxIterations
Whether we should reset the velocity to zero when the character exceeds the maximum collision iterations

### DetectObstructionsForParentBodyMovement
Enables doing a collider cast to detect obstructions when being moved by a parent body, instead of simply moving the character transform along

### ProcessStatefulCharacterHits
Enables storing stateful character hits (collision enter, exit, stay events)

____________________________________________________________________________________

## Dynamics

### SimulateDynamicBody
Enables physics interactions (push and be pushed) with other dynamic bodies

### Mass
The mass used to simulate dynamic body interactions

____________________________________________________________________________________

## Step Handling

### StepHandling
Enables step handling

### MaxStepHeight
The maximum height that a character can step

### ExtraStepCheckDistance
The horizontal distance, on both sides of the stepping collision point, where the character will raycast for finding grounded surfaces. Use this to allow stepping on slightly inclined steps

### UseMaxStepHeightForGroundSnappingDistance
Makes the character ground snapping distance be the same as the stepping distance. Use this to be able to step down as well as stepping up