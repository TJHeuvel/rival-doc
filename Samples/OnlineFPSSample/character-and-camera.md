Back to [Samples](../../samples.md)

# OnlineFPS Sample - Character and Camera


## Systems

Character movement logic is all part of the `GhostPredictionSystemGroup`, and update after the `OnlineFPSPlayerControlSystem` which handles telling the character how to move based on player commands.

`OnlineFPSCharacterMovementSystem` handles translation movement, and actually updates under the `PredictedPhysicsSystemGroup`. It does so because it has dependencies on the physics systems.

`OnlineFPSCharacterRotationSystem` handles rotation of both the character entity and the "View" entity (where the camera + weapon are). It updates directly in `GhostPredictionSystemGroup`.

Both systems make sure to call `GhostPredictionSystemGroup.ShouldPredict` before running the character update. We must only update the character logic if we must predict.

The actual rendered camera is a GameObject in the scene (named "GameObjectCamera"), but it constantly copies the transform of the entity marked as being the main camera entity. The GameObject camera registers itself as the rendering camera with the `MainGameObjectCamera` monobehaviour, and the `MainCameraSystem` handles setting its transform accordingly. 


## Ghost setup 

There are two main prefabs that are important to character control. They are under "Rival_Samples/Sample_OnlineFPS/Prefabs/Ghosts":
- FPSCharacter: This is actual controlled character
- Player: this represents a human player controlling a target character

Both of these prefabs must have a `GhostAuthoringComponent` and a `GhostOwnerComponentAuthoring`, and their "Default Ghost Mode" must be set to "Predicted". This setup is so that all players and characters in the game use a concept known as [remote player prediction](https://docs.unity3d.com/Packages/com.unity.netcode@0.50/manual/prediction.html#remote-players-prediction). What this means is that every player & character in the game, regardless of whether or not they are owned by the local player, will be using prediction.

Here are the main ghost fields for the "FPSCharacter" prefab:
* `Translation.Value` (in this sample, we make this have no quantization, to prevent jitter)
* `Rotation.Value` (in this sample, we make this have no quantization, to prevent jitter)
* `KinematicCharacterBody.RelativeVelocity`
* `KinematicCharacterBody.IsGrounded`
* `KinematicCharacterBody.ParentEntity`
* `ActiveWeapon.WeaponEntity`
* `Health.CurrentHealth`
* `OnlineFPSCharacterComponent.ViewPitchDegrees`
* `OnlineFPSCharacterComponent.CameraTileAngle`


Here are the main ghost fields for the "Player" prefab:
* `OnlineFPSPlayer.ControlledEntity`
* `OnlineFPSPlayer.PlayerName`
* `OnlineFPSPlayerCommands` (all fields in this component)



## Resources

See [NetCode Manual](https://docs.unity3d.com/Packages/com.unity.netcode@latest) for more information on ghosts and ghost overrides

See [Networking](../../How_To/networking.md) for more details on what to sync over network for characters