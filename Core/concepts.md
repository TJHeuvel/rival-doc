Back to [Core](../core.md)

# Concepts

## Grounding

In Rival, a character is considered grounded (`KinematicCharacterBody.IsGrounded`) if its `KinematicCharacterBody.GroundHit` satisfies the requirements of your implementation of `IsGroundedOnHit` in your character processor. A `GroundHit` can exist even if `IsGrounded` is false. For example, if the character is on a very steep slope and sliding down, `GroundHit` will be the slope hit, but `IsGrounded` will be false. The default `IsGroundedOnHit` implementation is based on slope angle and the character's velocity relatively to the ground entity's velocity (to prevent the character from being grounded if its velocity is escaping the ground entity's velocity when the character is already in air).

`GroundHit` can be detected in two ways:
* by `KinematicCharacterUtilities.GroundingUpdate`
* by `KinematicCharacterUtilities.MovementAndDecollisionsUpdate`: if the `ConstrainVelocityToGroundPlane` option of your generated character component is false, it will always replace the `KinematicCharacterBody.GroundHit` by any detected hit. If it is true, it will only replace it if the character would be grounded on that hit. See [this page](../How_To/constraining-movement-to-ground.md) for additional details on this.

Moreover, every `KinematicCharacterHit` detected by the character has its own `IsGroundedOnHit`. This is used internally by the character update to determine if the character should slide towards the sides of a steep slope, if we should replace the `GroundHit`, etc...

Grounding can have various effects on character movement, depending on the various options in `KinematicCharacterBody`:
* if `KinematicCharacterBody.SnapToGround` is true, the character will constantly adjust its position to "snap" right back to the ground whenever there is any distance between it and the ground hit
* `ConstrainVelocityToGroundPlane` option of your generated character component is true, the character will not bump into the air when moving towards a slope that is too steep, and will instead slide towards the sides while sticking to the ground. If that option is false, the character will act more like a regular rigidbody and will bump up into the air
* The standard code generated character grounded velocity control handles velocity differently based on whether the character is grounded or not. Moreover, `CharacterControlUtilities.StandardGroundMove_Interpolated` will always reorient the target velocity on the ground plane by default.  
* When the character needs to decollide itself from a hit where it is considered grouded, it will decollide only in the direction of its `GroundingUp`. This helps with standing on moving platforms that can rotate around all axis, without having the character drift to the sides.


## Movement & Collision detection

Rival's character movement algirithm uses continuous collision detection to sweep the character's collider in the movement direction, detect hits, advance character position to the detected hit, project the character velocity on the hit normal, and then keep repeating these steps until all of the movement for this frame has been consumed, or until the maximum number of `KinematicCharacterBody.MaxContinuousCollisionsIterations` has been reached:

![](../Images/core-movement.png)

In this image, we have a top-down view of what happens when a character (green) wants to move by a given movement vector (blue) in a single frame. The character will start by casting its collider using the blue movement vector, and it will detect hit #1. It will then project its velocity vector against the hit, which will result in a new velocity represented by the first purple line. The character will then keep casting its collider again and repeat this process to detect hits #2, #3, and #4 and project its velocity again each time. Once it has reached hit #4 however, the internal character movement algorithm will detect that the character has reached a "blocking corner" and will instantly set velocity to zero, which will put an end to the movement iterations. All of this happens in a single frame.

After the character movement iterations are over, the character will detect any colliders it is currently overlapping with, and then it will decollide from them.


## Parent

You are not allowed to make the character entity be a child of another entity by using the regular parenting mechanic of the Transforms system. However, you can do it by setting the `KinematicCharacterBody.ParentEntity`.

Note that `KinematicCharacterUtilities.ParentMovementUpdate` has a last parameter named `constrainRotationToGroundingUp`. When this parameter is true, the character will not be rotated regularly by its parent's rotation, but will instead force itself to keep an upright rotation (towards its `GroundingUp`). This is useful for moving platform logic, but you may want to change this for other parenting scenarios.

Internally, the default moving platforms detection update (`KinematicCharacterUtilities.DefaultMethods.MovingPlatformDetection`) uses this `ParentEntity`. When a valid moving platform is detected, this function sets the `ParentEntity` to this platform's entity. If not; it sets the `ParentEntity` to null. If you wish to handle the parent entity manually, you should remove the line that calls `KinematicCharacterUtilities.DefaultMethods.MovingPlatformDetection` first, so it doesn't overwrite your changes every frame.


