Back to [Samples](../../samples.md)

# Platformer Sample - Character

## Character

The character in this sample is fairly complex. Due to its level of complexity, we've decided to separate its movement logic into various "states".

`PlatformerCharacterProcessor.OnUpdate` starts by handling logic that is common to all states, then calls the current state's update via the `PlatformerCharacterStateMachine` component, and finally does more logic common to all states.

To allow more flexibility in state logic, the usual core character update methods are called by the individual state updates, rather than by the `PlatformerCharacterProcessor.OnUpdate` directly. `PlatformerCharacterProcessor` also contains various utility functions that could be used by several states. 


## State Machine

`PlatformerCharacterStateMachine` holds structures corresponding to all character states, remembers which state is the current one, and contains functions to call `OnStateEnter`, `OnStateExit`, and `OnStateUpdate`. These functions work by doing a switch case over the `CharacterState` enum, and calling the right function in the state struct corresponding to that `CharacterState`.

The `PlatformerCharacterProcessor` passes itself by reference to all state function calls, so that these state functions can have access to all the data that the processor holds.

State transitions are detected by the state themselves in their update, and the transition is done by calling `PlatformerCharacterProcessor.TransitionToState`.


## States

Here are the various states of the character:

- [Ground Move](CharacterStates/ground-move.md)
- [Air Move](CharacterStates/air-move.md)
- [Crouched](CharacterStates/crouched.md)
- [Wall Run](CharacterStates/wall-run.md)
- [Flying](CharacterStates/flying.md)
- [Dashing](CharacterStates/dashing.md)
- [Rope Swing](CharacterStates/rope-swing.md)
- [Rolling](CharacterStates/rolling.md)
- [Swimming](CharacterStates/swimming.md)
- [Climbing](CharacterStates/climbing.md)
- [Ledge Grab](CharacterStates/ledge-grab.md)
- [Disabled](CharacterStates/disabled.md)


## Misc Features

Here is a quick description of how several other features were implemented:

**Planet Gravity**: The gravity of all physics objects in this sample is handled by a `CustomGravity` component, a `GravityZonesSystem`, and special gravity zones such as `GlobalGravityZone` and `SphericalGravityZone`. The `GravityZonesSystem` will handle calculating a spherical gravity to all `CustomGravity` entities in its sphere trigger zone. Then, it will apply a global gravity to all `CustomGravity` entities that are not in any gravity zone. Finally, for all dynamic body entities that have a `CustomGravity` and a `PhysicsVelocity`, it will add that calculated gravity to the `PhysicsVelocity.Linear`. The character doesn't get its velocity modified even though it has a `CustomGravity`, because it is not a dynamic body. The character takes care of adding its custom gravity to its `KinematicCharacterBody.RelativeVelocity` in its state updates. And finally, the character orients its rotation so that it always points towards the opposite of the custom gravity's direction.

**Ice Surface**: A `CharacterFrictionModifier` component can be placed on rigidbodies, to modify the character's simulated friction when it walks on that surface. In its state movement update, the character tries to see if the ground hit entity has a `CharacterFrictionModifier` component, and if so, it'll change the way it controls its velocity based on the `CharacterFrictionModifier.Friction`

**Wind Zone**: A `WindZoneSystem` iterates on all entities that have a `WindZone` component and a trigger collider that raises trigger events. For each teleporter, if a trigger event with a `KinematicCharacterBody` was detected, we add a wind acceleration to the `KinematicCharacterBody.RelativeVelocity`

**Jump Pad**: A `JumpPadSystem` iterates on all entities that have a `JumpPad` component and a trigger collider that raises trigger events. For each teleporter, if a trigger event with a `KinematicCharacterBody` was detected, we call `KinematicCharacterBody.Unground()` on the character entity, and also add a jump velocity to the `KinematicCharacterBody.RelativeVelocity`

**Teleporter**: A `TeleporterSystem` iterates on all entities that have a `Teleporter` component and a trigger collider that raises trigger events. For each teleporter, if a trigger event with a `KinematicCharacterBody` was detected, we move the character `Translation` to the teleportation destination point. Additionally, since we don't want character interpolation to be active for the teleportation, we call `SkipNextInterpolation()` on the `CharacterInterpolation` component of the character entity. This will skip the interpolation for the remainder of the frames until next fixed update.

**Sticky Surfaces (walk on walls)**: `PlatformerCharacter.StickySurfaceTag` allows us to assign a physics tag that represents sticky surfaces that the character can walk on. In the character's state update, it will see if the rigidbody of the ground hit entity has that tag, and if so, it will orient its rotation so that it always points towards the ground normal. Moreover, since the character's `GroundingUp` is always set to be the character rotation's up direction, our grounding reference direction will always be relative to the character orientation, which means the character could consider itself grounded even if it was standing upside down on the ceiling.
