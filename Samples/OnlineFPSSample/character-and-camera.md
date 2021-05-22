

# OnlineFPS Sample - Character and Camera

The networking of the character is all done in `OnlineFPSCharacterMovementSystem`. Here are the key points when it comes to networking character controllers:
* The character system must update in the `GhostPredictionSystemGroup`
* The CollisionWorld used by the character update must be a collision world that represents the world at the time where the predicting tick happened. In this case, we created a `PhysicsPredictionSystem` that rebuilds the PhysicsWorld at every predicting tick, and the character update uses that.
* We make sure to call `GhostPredictionSystemGroup.ShouldPredict` before running the character update. We must only update the character logic if we must predict.
* We make sure to use the player commands (`OnlineFPSPlayerCommands`) as inputs for the character movement.

Since this is a first-person game, the camera entity is a child of the character entity. It is controlled by calculating the desired horizontal and vertical angles, and applying them to the camera transform as a rotation.

Instead of updating the character rotation in the usual character system that updates on a fixed time step, we update it in a separate system that updates at a variable time step: `OnlineFPSCharacterVariableUpdateSystem`. We want camera movement to be as smooth and responsive as possible, and updating it at a fixed time step, even if interpolated, would result in camera movement that isn't as good as it could be

The actual rendered camera is a GameObject in the scene (named "GameObjectCamera"), but it constantly copies the transform of the entity marked as being the main camera entity. The GameObject camera registers itself as the rendering camera with the `RegisterAsMainCameraBehaviour` monobehaviour, and the `MainCameraSystem` handles setting its transform accordingly. 

## Synchronized Fields

There is an `OnlineFPSGhostOverrides` class in the project that defines what component fields get synced, and how they get synced. It declares that the following fields must be synced:
* `Translation.Value`
* `Rotation.Value`
* `KinematicCharacterBody.RelativeVelocity`
* `KinematicCharacterBody.IsGrounded`
* `KinematicCharacterBody.ParentEntity`

See [NetCode Manual](https://docs.unity3d.com/Packages/com.unity.netcode@0.6/manual/ghost-snapshots.html) for more information on ghosts and ghost overrides

See [Networking](../../How_To/networking.md) for more details on what to sync over network for characters