
# Tips

## Particularities
* Always make sure the root character entity has (1,1,1) scale. You are allowed to scale child entities
* The character entity (the one with the `KinematicCharacterBody` component) must never be a child of another entity
* All physics bodies that the character can stand on, or all bodies that can be set as the character's `ParentEntity`, must have a `TrackedTransform` component in order to work properly
* You must enable the `SynchronizeCollisionWorld` option on the PhysicsStep component if you want to eliminate jitter and visual lag when moving against other moving bodies


## Performance Tips

- **Convex colliders and low polygon counts**: For the level geometry, use convex collider shapes whenever possible, and make sure your non-convex colliders have as little polygons as possible. It is standard practice in games to create simplified collision meshes for mesh objects.
- **Velocity that is parallel to ground**: In your character control code where you assign a velocity to the character, it is always a good idea to assign a velocity that is perfectly parallel to the detected ground normal. This can potentially save you one collider cast iteration that would've detected a hit with the ground if the velocity weren't parallel
- **Disabling the physics step**: This character doesn't need any physics step in order to function properly. You can safely set the "Simulation Type" to "None" on the Physics Step component, if you don't need any physics step for physics bodies other than characters.
- **Small ground probing/snap distances**: In order to detect grounding, the character does a downward collider cast with the length `KinematicCharacterBody.GroundSnappingDistance`. You should keep this value as small as possible to get the job done, because the longer the collider cast, the heavier it can be to process. 
- **Character iteration counts**: Don't assign iteration counts that are too high for `KinematicCharacterBody.MaxContinuousCollisionsIterations` and `KinematicCharacterBody.MaxOverlapDecollisionIterations`. If in doubt, keep the defaults
- **`KinematicCharacterBody.AddCloseHitsForVelocityProjection`**: Enabling this option on the character body adds one CalculateDistance call per character. Only activate it if you need it (it can help prevent tunnelling when your character has a shape that changes its collisions when it is rotated).
- **`KinematicCharacterBody.ProcessStatefulCharacterHits`**: Enabling this option on the character body adds an additional step of classifying character stateful hits (Enter/Exit/Stay). This can add a slight performance cost
- **`KinematicCharacterBody.DetectObstructionsForParentBodyMovement`**: Enabling this option on the character body adds one CastCollider call per character when the character has a "ParentEntity" set.
- **Denivelations**: If you have a character Processor that calls `KinematicCharacterUtilities.DefaultMethods.ShouldPreventGroundingBasedOnDenivelations` in order to detect denivelations, be aware that this function has a notable performance impact, because it does a few raycast queries. Make sure you only call this when necessary.
- **Step Handling**: Implementing step handling on the character adds the cost of potentially up to 3 collider casts on each hit that is considered non-grounded, and a few additional raycasts if you are implementing step handling for a round-bottom character. Keep this in mind.
