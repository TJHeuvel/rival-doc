---
layout: default
title: Networking
parent: How-To
nav_order: 5
---

# Networking

(for a full example of predicted character networking, see the OnlineFPS sample)

## Synchronized data

Predicted character networking typically requires the following fields to be synchronized over network. This is the data that represents the initial character state that will always result in the same outcome after you run a character update with it:
* `Translation`
* `Rotation`
* `KinematicCharacterBody.RelativeVelocity`
* `KinematicCharacterBody.IsGrounded`
* `KinematicCharacterBody.ParentEntity` (only necessary if you use the ParentEntity mechanism in your game, like for moving platforms)

The rest of the fields in `KinematicCharacterBody` typically don't need to be synchronized unless you manually modify them. For example, you don't need to synchronize `KinematicCharacterBody.DetectGrounding` unless you manually set it to a different value at some points in your code. This field doesn't change by itself otherwise.

Several other fields of `KinematicCharacterBody`, such as `GroundHit`, `WasGroundedBeforeCharacterUpdate`, `ParentVelocity`, `PreviousParentEntity`, etc... are all calculated entirely during the frame, and therefore never need to be synchronized. However, you need to make sure that none of your game code accesses those values before `KinematicCharacterUtilities.InitializationUpdate` has run (this is where they are reset).

## Prediction loop

In order to include the character logic in DOTS NetCode's prediction loop, the following steps are necessary:
* The character system must update in the `GhostPredictionSystemGroup`
* The CollisionWorld used by the character update must be a collision world that represents the world at the time where the predicting tick happened. You can either use NetCode's `PhysicsWorldHistory` to get the CollisionWorld at the predicted tick, or create a system in the prediction group that rebuilds the `PhysicsWorld` before the character update
* Make sure to call `GhostPredictionSystemGroup.ShouldPredict` before running the character update. We must only update the character logic if we must predict.
* Make sure to use the player commands from the predicted tick as inputs for the character movement.

## Networking moving platforms

todo: will come at a later date