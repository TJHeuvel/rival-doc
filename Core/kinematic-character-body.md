Back to [Core](../core.md)

# Kinematic Character Body

The `KinematicCharacterBody` is the core component that represents the character. It contains properties that affect how the character does its update, as well as input/output values representing character velocity, grounding, etc...


## General Properties

The following fields are all properties that are never changed by the core update loop.

- `CustomPhysicsBodyTags`: Physics tags to be added to the character's physics body
- `UseInterpolation`: Enables interpolating the character's transform between fixed update steps, for smoother movement
- `EvaluateGrounding`: Enables all logic related to detecting fround and evaluating if we are grounded on hits
- `SnapToGround`: Enables snapping to the ground surface below the character
- `GroundSnappingDistance`: The distance that the character will try to snap to the ground surface below it
- `MaxGroundedSlopeAngle`: The max slope angle that the character can be considered grounded on
- `DetectMovementCollisions`: Enables detecting and solving movement collisions with a collider cast, based on character's velocity
- `DecollideFromOverlaps`: Enables detecting and solving overlaps
- `AddCloseHitsForVelocityProjection`: Enables doing an extra physics check to project velocity on initial overlaps before the character moves. This can help with tunneling issues with non-circular character colliders, but has a performance cost.
- `MaxContinuousCollisionsIterations`: The maximum amount of times per frame that the character should try to cast its collider for detecting movement hits
- `MaxOverlapDecollisionIterations`: The maximum amount of times per frame that the character should try to decollide itself from overlaps
- `DiscardMovementWhenExceedMaxIterations`: Whether we should reset the remaining move distance to zero when the character exceeds the maximum collision iterations
- `KillVelocityWhenExceedMaxIterations`: Whether we should set the velocity to zero when the character exceeds the maximum collision iterations
- `DetectObstructionsForParentBodyMovement`: Enables doing a collider cast to detect obstructions when being moved by a parent body, instead of simply moving the character transform along
- `SimulateDynamicBody`: Enables physics interactions (push and be pushed) with other dynamic bodies
- `Mass`: The mass used to simulate dynamic body interactions


## Input & Output values

The following fields are all either assigned by the user as input to the character, and/or calculated by the character update during the frame:

- `RelativeVelocity`: The worldspace character velocity relative to its `ParentEntity`'s velocity. If no `ParentEntity` is assigned, it simply represents the final velocity. This value is set by the user to control the character, but it also gets changed during the character's update when velocity must be projected on obstructions
- `IsGrounded`: Whether the character is currently grounded or not
- `WasGroundedBeforeCharacterUpdate`: Whether the character was grounded or not before the character update began. Used internally by the character update, but could also be used to detect landing or leaving ground
- `GroundHit`: The character's current ground hit, if any. Note that a ground hit can exist even if `IsGrounded` is false (it could be a hit where the slope is too steep, for example)
- `ParentEntity`: The character's parent entity. When a parent entity is set, the character will move along with it as though it was parented to it. This is typically used for moving platform logic
- `ParentVelocity`: The velocity of the current `ParentEntity`, if any
- `RotationFromParent`: The rotation that was caused on the character due to the rotation of its parent on this update
- `PreviousParentEntity`: The previous `ParentEntity`. Used internally by the character update