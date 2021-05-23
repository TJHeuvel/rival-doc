Back to [Core](../core.md)

# Character Code Structure

Assumming we chose "MyCharacter" as the character name in the **Character Controller Wizard**, these are the structs & classes that will be generated:


## MyCharacterComponent and MyCharacterInputs

These components define the base ECS components that are specific to your character. 

`MyCharacterComponent` contains character movement parameters such as ground move speed, air move speed, gravity, etc...

`MyCharacterInputs` is what the entity that is controlling the character (human controller or AI controller) uses to tell the character how to move:
* `WorldMoveVector`: a worldspace vector of length 0 to 1, representing the direction & scale of the desired movement
* `JumpRequested`: if the character should jump


## MyCharacterSystem

The `MyCharacterSystem` schedules a `MyCharacterJob` that iterates on the component types that represent your character. For each character, the `MyCharacterJob` creates a temporary `MyCharacterProcessor` struct, initializes all of its data, calls `MyCharacterProcessor.OnUpdate`, and finally writes back updated data from the `MyCharacterProcessor` to the components.

In short, the role of `MyCharacterSystem` and `MyCharacterJob` is to do all of the setup required to call `MyCharacterProcessor.OnUpdate`.


## MyCharacterProcessor

`MyCharacterProcessor` is a struct implementing `IKinematicCharacterProcessor` that is passed to various `KinematicCharacterUtilities` functions during the character update loop in `MyCharacterJob`. Its purpose is to:
- Hold data that is common to most character update steps.
- Implement the character update.
- Recieve "callbacks" from the core character update loop where you can customize some of the logic that happens there.

#### Processor Update

`MyCharacterProcessor.OnUpdate` will call the various core character update steps, in the order that they must be called. See [Utilities](utilities.md) for an in-depth description of these calls.

`MyCharacterProcessor.HandleCharacterControl` contains basic character control logic, and you are free to customize or replace this entirely with your own implementation. This function typically has 2 main goals: controlling the `KinematicCharacterBody.RelativeVelocity` of the character, and controlling the `Rotation` of the character. The default implementation basically does this:
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

#### Processor Callbacks

Here is a description of the processor callbacks:
- `CanCollideWithHit`: return true if the character should be able to collide with the hit, and return false if not. By default, we call `KinematicCharacterUtilities.DefaultMethods.CanCollideWithHit` to avoid colliding with trigger colliders, but you could add additional conditions.
- `OnMovementHit`: determine what happens when the character collider casts have detected a hit during the movement iterations. By default, we call `KinematicCharacterUtilities.DefaultMethods.OnMovementHit`, which will handle moving the character forward towards the hit, consuming movement length, and projecting velocity. This callback is mostly for advanced usage, and it is recommended to not change it unless you wish to implement [step handling](../How_To/step-handling.md).
- `IsGroundedOnHit`: determine if the character is grounded on the hit or not. By default, we call `KinematicCharacterUtilities.DefaultMethods.IsGroundedOnHit`, which will check the slope angle and velocity direction in order to determine the final result.
- `OverrideDynamicHitMasses`: Gives you an opportunity to modify the mass ratios between the character and another dynamic body when they are colliding. This is only called for characters that have [SimulatedDynamic](../How_To/dynamic-body-interaction.md) set to true. This function can be left empty if you do not wish to modify anything.
- `ProjectVelocityOnHits`: determines how the character velocity gets projected on hits, based on all hits so far this frame. By default, we call `KinematicCharacterUtilities.DefaultMethods.ProjectVelocityOnHits`. This is callback mostly for advanced usage, and it is recommended not to change it unless you wish to make your character bounce on certain surfaces, for example.

It is recommended to carefully study & understand the "default methods" called here before attempting to modify them. **Changing some of these things could break the character movement** or have a negative impact on performance, especially for the default implementations of `OnMovementHit` and `ProjectVelocityOnHits`.


## MyCharacterAuthoring

`MyCharacterAuthoring` is the authoring component that will add all the necessary ECS components to the character entity, and will make checks to validate that everything is set up correctly. In the inspector, it will allow you to edit the `AuthoringKinematicCharacterBody` data (the core parameters of the character controller), as well as your `MyCharacterComponent` data.

Internally, this component will call `KinematicCharacterUtilities.HandleConversionForCharacter`, which handles setting up the character entity correctly with all the required components. It is also important that the conversion happens after the `EndColliderConversionSystem`, but the generated `MyCharacterAuthoring` already handles this.

The components added to the entity during conversion are:
- `KinematicCharacterBody`
- `MyCharacterComponent`
- `MyCharacterInputs`
- `PhysicsVelocity`
- `PhysicsMass`
- `PhysicsGravityFactor`
- `PhysicsCustomTags`
- (optional) `CharacterInterpolation`
- `DynamicBuffer<KinematicCharacterHit>`
- `DynamicBuffer<KinematicVelocityProjectionHit>`
- `DynamicBuffer<KinematicCharacterDeferredImpulse>`
- `DynamicBuffer<StatefulKinematicCharacterHit>`
