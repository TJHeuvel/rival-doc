---
layout: default
title: Dynamic Body Interactions
parent: How-To
nav_order: 4
---

# Dynamic Body Interactions
Allowing your character to push and be pushed by dynamic rigidbodies can be achieved by enabling the `SimulateDynamicBody` option in your character's authoring component (or `KinematicCharacterBody.SimulateDynamicBody` if you want to change it at runtime). When this is enabled, the character will apply force on itself and other bodies in order to immitate the behaviour of a true dynamic rigidbody. This uses the `Mass` property of the character's authoring component (or `KinematicCharacterBody.Mass`) to simulate collision mass ratios.

Since the character is still kinematic, by default, a dynamic rigidbody trying to push the character will be stopped by the character as soon as the collision happens. In order to solve this, you need to make sure that dynamic bodies will not be stopped by collisions with the character. Here are two ways to achieve this:
1. Set the character's PhysicsShape's collision response to either "None" or "RaiseTriggerEvents"
1. Create an `IBodyPairs` job that disable pairs between simulated dynamic characters and dynamic bodies. In the samples project, there is an example of this: `IgnorePhysicsStepCollisionsSystem`

The advantage of option 1 is that it is preferable for performance, and allows you to still raise trigger events with the collided bodies.

The advantage of option 2 is that the character remains a "collider" as opposed to a "trigger", which means that it will still be detectable by physics queries that expect to hit only colliders (not triggers). But using this method means that your character cannot raise collision events with dynamic bodies, so you'd have to rely on the character hits buffer in order to detect those.