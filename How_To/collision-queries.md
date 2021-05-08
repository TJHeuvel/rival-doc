---
layout: default
title: Collision Queries
parent: How-To
nav_order: 9
---

# Collision Queries
There are times when you might want to do a physics query that detects the hits that the character would truly collide with. This could involve a lot of work to do manually, because there are lots of rules that can go into determining which collisions are valid and which aren't (especially with the character processor where you can add some custom code to ignore certain collisions).

For that reason, `KinematicCharacterUtilities` comes with several utility functions that give you the filtered hits that represent only the hits that the character would truly collide with:
* `CastColliderClosestCollisions`
* `CastColliderAllCollisions`
* `RaycastClosestCollisions`
* `RaycastAllCollisions`
* `CalculateDistanceClosestCollisions`
* `CalculateDistanceAllCollisions`

These functions will take all factors into account, including the custom logic you might have implemented in your `CanCollideWithHit` function of the character processor