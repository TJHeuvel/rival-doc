

# Character Code Structure

Rival does not have any internal systems that contribute to the character movement update. Instead, all of the systems are generated and are fully exposed to you, so they can more easily be modified. Assumming we chose "MyCharacter" as the character name in the **Character Controller Wizard**, these are the structs & classes that will be generated:


## MyCharacterComponent and MyCharacterInputs

This defines the base ECS components that are specific to your character. You will have a `MyCharacterComponent` containing character movement parameters, and a `MyCharacterInputs` containing the inputs for the character. 

`MyCharacterInputs` should be seen as a common way for both player inputs or AI logic to control a character. It lets you implement the character movement logic only once, regardless of who controls it. For example, the player input systems and the AI systems would both be doing their separate calculations to determine what the `WorldMoveVector` and the `JumpRequested` should be, and they'd both write that data to `MyCharacterInputs`. Later, the `MyCharacterProcessor` update loop will use `MyCharacterInputs` to determine how the character should move, without having to worry about who is controlling it (player or AI). However, this way of handling things (having a common interface for players and AI to control the character) is only a suggestion; it is not necessary, and you can choose to do something else entirely instead if you want to.


## MyCharacterAuthoring

`MyCharacterAuthoring` is the authoring component that will add all the necessary ECS components to the character entity, and will make checks to validate that everything is set up correctly. By default, it will allow you to edit the `AuthoringKinematicCharacterBody` data (the core parameters of the character controller), as well as your `MyCharacterComponent` data in the inspector.

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


## MyCharacterSystem

`MyCharacterSystem` is in charge of scheduling the `MyCharacterJob`, and giving it all the data it will need during its update. It updates in `KinematicCharacterUpdateGroup`, which takes care of all basic update order considerations (update in fixed step group, after physics, before transforms).

The `MyCharacterJob` creates the core character update loop:
- Create a temporary `MyCharacterProcessor` structure that will hold all the data necessary for your character update.
- Call `MyCharacterProcessor.OnUpdate`, where the character logic is implemented.
- Write the `MyCharacterProcessor` data back to the ECS components. By default, the core character update only modifies `Translation`, `Rotation`, `KinematicCharacterBody`, `MyCharacterComponent`, and `MyCharacterInputs`. It's up to you to write to other components you may modify in your own implementation.


## MyCharacterProcessor

`MyCharacterProcessor` is a struct implementing `IKinematicCharacterProcessor` that is passed to various `KinematicCharacterUtilities` functions during the character update loop in `MyCharacterJob`. Its purpose is to:
- Hold data that is common to most character update steps.
- Implement the character update.
- Recieve "callbacks" from the core character update loop where you can customize some of the logic that happens there.

### Processor Update

`MyCharacterProcessor.OnUpdate` will call various core character update steps, as well as a `HandleCharacterControl` function where default velocity & rotation control behaviour is implemented. It is absolutely necessary that all the calls to `KinematicCharacterUtilities.(...)` done here happen in that exact order, even though some of them are optional and can be removed (the comments will tell you which ones). However, you are free to add additional calls to your own functions before, after, or in-between these. See [Utilities](utilities.md) for an in-depth description of these calls.

### Processor Callbacks

Here is a description of the processor callbacks:
- `CanCollideWithHit`: return true if the character should be able to collide with the hit, and return false if not. By default, we call `KinematicCharacterUtilities.DefaultMethods.CanCollideWithHit` to avoid colliding with trigger colliders, but you could add additional conditions.
- `OnMovementHit`: determine what happens when the character collider casts have detected a hit during the movement iterations. By default, we call `KinematicCharacterUtilities.DefaultMethods.OnMovementHit`, which will handle moving the character forward towards the hit, consuming movement length, and projecting velocity. This callback is mostly for advanced usage, and it is recommended to not change it unless you wish to implement [step handling](../How_To/step-handling.md).
- `IsGroundedOnHit`: determine if the character is grounded on the hit or not. By default, we call `KinematicCharacterUtilities.DefaultMethods.IsGroundedOnHit`, which will check the slope angle and velocity direction in order to determine the final result.
- `OverrideDynamicHitMasses`: Gives you an opportunity to modify the mass ratios between the character and another dynamic body when they are colliding. This is only called for characters that have [SimulatedDynamic](../How_To/dynamic-body-interaction.md) set to true. This function can be left empty if you do not wish to modify anything.
- `ProjectVelocityOnHits`: determines how the character velocity gets projected on hits, based on all hits so far this frame. By default, we call `KinematicCharacterUtilities.DefaultMethods.ProjectVelocityOnHits`. This is callback mostly for advanced usage, and it is recommended not to change it unless you wish to make your character bounce on certain surfaces, for example.

It is recommended to carefully study & understand the "default methods" called here before attempting to modify them. **Changing some of these things could break the character movement** or have a negative impact on performance, especially for the default implementations of `OnMovementHit` and `ProjectVelocityOnHits`.