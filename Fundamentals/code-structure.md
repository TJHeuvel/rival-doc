---
layout: default
title: Character Code Structure
parent: Fundamentals
nav_order: 2
---

A

# Character Code Structure

Rival does not have any internal systems that contribute to the character movement update. Instead, all of the systems are generated and are fully exposed to you, so they can more easily be modified. Assumming we chose "MyCharacter" as the character name in the [Character Creation Wizard](creating-a-character), these are the files that will be generated and the roles they have:


## MyCharacterComponent.cs

This defines the base ECS components that are specific to your character. You will have a `MyCharacterComponent` containing character movement parameters, and a `MyCharacterInputs` containing the inputs for the character. 

`MyCharacterInputs` should be seen as a common way for both player inputs or AI logic to control a character. It lets you implement the character movement logic only once, regardless of who controls it. For example, the player input systems and the AI systems would both be doing their separate calculations to determine what the `WorldMoveVector` and the `JumpRequested` should be, and they'd both write that data to `MyCharacterInputs`. Later, the `MyCharacterImplementation` update loop will use `MyCharacterInputs` to determine how the character should move, without having to worry about who is controlling it (player or AI). However, this way of handling things (having a common interface for players and AI to control the character) is only a suggestion; it is not necessary, and you can choose to do something else entirely instead if you want to.


## MyCharacterAuthoring.cs

`MyCharacterAuthoring` is the authoring component that will add all the necessary ECS components to the character entity, and will make checks to validate that everything is set up correctly. By default, it will allow you to edit the `AuthoringKinematicCharacterBody` data (the core parameters of the character controller), as well as your `MyCharacterComponent` data.

Internally, this component will call `KinematicCharacterUtilities.HandleConversionForCharacter`, which handles setting up the character entity correctly with all the required components. It is also important that the conversion happens after the `EndColliderConversionSystem`, but the generated `MyCharacterAuthoring` already handles this.


## MyCharacterSystem.cs

`MyCharacterSystem` is in charge of scheduling the `MyCharacterJob`, and giving it all the data it will need during its update. It updates in `KinematicCharacterUpdateGroup`, which takes care of all basic update order considerations (update in fixed step group, after physics, before transforms).

The `MyCharacterJob` creates the core character update loop:
- Create a temporary `MyCharacterUpdateData` structure that will hold all the data necessary for your character update .
- Call `KinematicCharacterUtilities.InitializationUpdate`: clears some variables and buffers at the start of the update.
- Call `KinematicCharacterUtilities.ParentMovementUpdate`: handles moving the character based on its currently-assigned `KinematicCharacterBody.ParentEntity`, if any.
- Call `KinematicCharacterUtilities.GroundingUpdate`: handles detecting character grounding.
- Call `MyCharacterImplementation.BeforeCharacterMove`: a callback meant to be user-implemented in `MyCharacterImplementation`. This is typically where you will assign a desired velocity and rotation to your character. It is done here, because it is right after detecting up-to-date grounding (which often affects velocity), and before actually solving the character movement collisions.
- Call `KinematicCharacterUtilities.MovementAndDecollisionsUpdate`: handles moving the character and solving collisions, based on `KinematicCharacterBody.RelativeVelocity`, rotation, character grounding, and various other properties in `KinematicCharacterBody`.
- Call `MyCharacterImplementation.AfterCharacterMove`: a callback meant to be user-implemented in `MyCharacterImplementation`. Gives you an opportunity to do something after character movement has been solved.
- Call `KinematicCharacterUtilities.ParentMomentumUpdate`: handles preserving velocity momentum when getting unparented from a parent body (such as a moving platform).
- Call `KinematicCharacterUtilities.ProcessStatefulCharacterHits`: handles filling the `StatefulKinematicCharacterHit` buffer on the character entity, with character hits that have an Enter/Exit/Stay state associated to them
- Write data back to the ECS components. By default, the core character update only modifies Translation, Rotation, and KinematicCharacterBody. It's up to you to write to other components you may modify in your own implementation.

It is absolutely necessary that all the calls to `KinematicCharacterUtilities.(...)` mentioned in the list above happen in that exact order. Otherwise, you may start seeing bugs. However, you are free to add additional calls to your own functions before, after, or in-between these.

You will typically need access to extra data that is specific to your game in the `MyCharacterJob`. In that case, Follow these steps:
- add additional `ComponentDataFromEntity` and/or `ComponentTypeHandle` fields to the job struct
- set that data when scheduling the job in `MyCharacterSystem.OnUpdate`
- (optionally) add this data to the `MyCharacterUpdateData` struct in the job, for the specific character being processed. This is an easy way to make that data available to your user-implemented methods in `MyCharacterImplementation`


## MyCharacterProcessor.cs

`MyCharacterProcessor` is a struct implementing `IKinematicCharacterProcessor` that is passed to various `KinematicCharacterUtilities` functions during the character update loop in `MyCharacterJob`. Its purpose is to recieve "callbacks" from the core character update loop, and to let you customize some of the logic that happens there:
- `CanCollideWithHit`: return true if the character should be able to collide with the hit, and return false if not. By default, we call `KinematicCharacterUtilities.DefaultMethods.CanCollideWithHit` to avoid colliding with trigger colliders, but you could add additional conditions.
- `OnMovementHit`: determine what happens when the character collider casts have detected a hit during the movement iterations. By default, we call `KinematicCharacterUtilities.DefaultMethods.OnMovementHit`, which will handle moving the character forward towards the hit, consuming movement length, and projecting velocity. This callback is mostly for advanced usage, and it is recommended to not change it unless you wish to implement [step handling](step-handling).
- `IsGroundedOnHit`: determine if the character is grounded on the hit or not. By default, we call `KinematicCharacterUtilities.DefaultMethods.IsGroundedOnHit`, which will check the slope angle and velocity direction in order to determine the final result. If you wish to implement [step handling](step-handling) for characters with a rounded-bottom shape such as a capsule, you would have to call `KinematicCharacterUtilities.DefaultMethods.IsGroundedOnHit_ForRoundedColliderStepHandling` instead.
- `OverrideDynamicHitMasses`: Gives you an opportunity to modify the mass ratios between the character and another dynamic body when they are colliding. This is only called for characters that have [SimulatedDynamic](dynamic-body-interaction) set to true. This function can be left empty if you do not wish to modify anything.
- `ProjectVelocityOnHits`: determines how the character velocity gets projected on hits, based on all hits so far this frame. By default, we call `KinematicCharacterUtilities.DefaultMethods.ProjectVelocityOnHits`. This is callback mostly for advanced usage, and it is recommended not to change it unless you wish to make your character bounce on certain surfaces, for example.

It is recommended to carefully study & understand the "default methods" called here before attempting to modify them, if you do wish to modify them. Changing some of these things could break the character movement, especially `OnMovementHit` and `ProjectVelocityOnHits`.

If you would need your `MyCharacterProcessor` to have access to custom data during some of these callbacks, simply add fields for that data in the `MyCharacterProcessor` struct. And then, in your `MyCharacterJob` in `MyCharacterSystem`, you can set that data in the processor struct before calling the various functions of the character update loop.


## MyCharacterImplementation.cs

`MyCharacterImplementation` is where most of the user-implemented logic of the character update will happen. It is also where the `MyCharacterUpdateData` struct is defined (this struct is meant to hold all the necessary data for the update of a character).

The default generated code will handle handle the following:
- basic character movement and ground pushing in `MyCharacterImplementation.BeforeCharacterMove`. Based on grounding, we assign the correct velocity to `KinematicCharacterBody.RelativeVelocity`, and we assign the desired character rotation at the end.
- moving platform detection in `MyCharacterImplementation.AfterCharacterMove`. See [Moving Platforms](moving-platforms) for more details.

All of this is meant to be customized and/or replaced by user code. It is only there as an example.