Back to [Core](../core.md)

# Character Processor

The Character Processor is a struct implementing `IKinematicCharacterProcessor` that all Rival characters use. Its purpose is to:
- Hold data that is common to most character update steps.
- Implement the character update.
- Recieve "callbacks" from the core character update loop where you can customize some of the logic that happens there.

The Character Processor is typically created and updated by the character update job.

In this section, we will assume we are working with a character processor struct named `MyCharacterProcessor`. However, in the [Standard Characters](/standard-characters.md), they are named `FirstPersonCharacterProcessor` and `ThirdPersonCharacterProcessor`.


## Processor Update

`MyCharacterProcessor.OnUpdate` is called by the character job, and will call the various core character update steps in the correct order. See [Utilities](utilities.md) for an in-depth description of these calls.

`MyCharacterProcessor.HandleCharacterControl` contains basic character control logic, and you are expected to customize or replace this entirely with your own implementation. This function typically has 2 main goals: controlling the `KinematicCharacterBody.RelativeVelocity` of the character, and controlling the `Rotation` of the character. The default implementation basically does this:
```cs
// if the character is grounded...
    // interpolate velocity towards input direction for responsive ground control
    // add jump velocity if jump input is requested
// otherwise, if the character is not grounded...
    // accelerate velocity towards input direction for air control
    // add gravity
    // apply drag

// rotate the character towards its move direction (if there is any move input)

// reset the jump input request
```


## Processor Callbacks

Here is a description of the processor callbacks:
- `CanCollideWithHit`: return true if the character should be able to collide with the hit, and return false if not. By default, we call `KinematicCharacterUtilities.DefaultMethods.CanCollideWithHit` to avoid colliding with trigger colliders, but you could add additional conditions.
- `OnMovementHit`: determine what happens when the character collider casts have detected a hit during the movement iterations. By default, we call `KinematicCharacterUtilities.DefaultMethods.OnMovementHit`, which will handle moving the character forward towards the hit, consuming movement length, projecting velocity, and handling stepping up obstacles.
- `IsGroundedOnHit`: determine if the character is grounded on the hit or not. By default, we call `KinematicCharacterUtilities.DefaultMethods.IsGroundedOnHit`, which will check the slope angle and velocity direction in order to determine the final result.
- `OverrideDynamicHitMasses`: Gives you an opportunity to modify the mass ratios between the character and another dynamic body when they are colliding. This is only called for characters that have [SimulatedDynamic](../How_To/dynamic-body-interaction.md) set to true. This function can be left empty if you do not wish to modify anything.
- `ProjectVelocityOnHits`: determines how the character velocity gets projected on hits, based on all hits so far this frame. By default, we call `KinematicCharacterUtilities.DefaultMethods.ProjectVelocityOnHits`. This is callback mostly for advanced usage, and it is recommended not to change it unless you wish to make your character bounce on certain surfaces, for example.

It is recommended to carefully study & understand the "default methods" called here before attempting to modify them. **Changing some of these things could break the character movement** or have a negative impact on performance, especially for the default implementations of `OnMovementHit` and `ProjectVelocityOnHits`.


