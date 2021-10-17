
# Tips

## Particularities
* Always make sure the root character entity has (1,1,1) scale. You are allowed to scale child entities though
* The character entity (the one with the `KinematicCharacterBody` component) must never be a child of another entity
* All physics bodies that the character can stand on, or all bodies that can be set as the character's `ParentEntity`, must have a `TrackedTransform` component in order to work properly
* You must enable the `SynchronizeCollisionWorld` option on the PhysicsStep component if you want to eliminate jitter and visual lag when moving against other moving bodies


## Performance Tips

- **Convex level colliders and low collider polygon counts**: For the level geometry, use convex collider shapes whenever possible, and make sure your non-convex colliders have as little polygons as possible. It is standard practice in games to create simplified collision meshes for mesh objects.
- **Disabling the physics step**: This character doesn't need any physics step in order to function properly. You can safely set the "Simulation Type" to "None" on the Physics Step component, if you don't need any physics step for physics bodies other than characters.
- **Small ground probing/snap distances**: In order to detect grounding, the character does a downward collider cast with the length `KinematicCharacterBody.GroundSnappingDistance`. You should keep this value as small as possible to get the job done, because the longer the collider cast, the heavier it can be to process. 
- **Character iteration counts**: Don't assign iteration counts that are too high for `KinematicCharacterBody.MaxContinuousCollisionsIterations` and `KinematicCharacterBody.MaxOverlapDecollisionIterations`. If in doubt, keep the defaults
- **`KinematicCharacterBody.AddCloseHitsForVelocityProjection`**: Enabling this option on the character body adds one CalculateDistance call per character. Only activate it if you need it (it can help prevent tunnelling when your character has a shape that changes its collisions when it is rotated).
- **`KinematicCharacterBody.DetectObstructionsForParentBodyMovement`**: Enabling this option on the character body adds one CastCollider call per character when the character has a "ParentEntity" set.
- **Slope Angle Changes Detection**: Enabling either `PreventGroundingWhenMovingTowardsNoGrounding` or `HasMaxDownwardSlopeChangeAngle` on the standard characters has a notable performance impact, because it adds a few raycast queries per character. Make sure you only use this when necessary.
- **Step Handling**: Enabling step handling on the standard characters adds the cost of potentially up to 3 collider casts on each hit that is considered non-grounded, and a few additional raycasts. Keep this in mind.
- **Turn off "JobsDebugger" in editor**:  This option has a high performance impact when playing in-editor. Turn it off via Unity's top-bar menu: "Jobs > JobsDebugger"
