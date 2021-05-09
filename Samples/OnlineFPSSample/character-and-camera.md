

# OnlineFPS Sample - Character and Camera

The networking of the character is all done in `OnlineFPSCharacterMovementSystem`. Here are the key points when it comes to networking character controllers:
* The character system must update in the `GhostPredictionSystemGroup`
* The CollisionWorld used by the character update must be a collision world that represents the world at the time where the predicting tick happened. In this case, we created a `PhysicsPredictionSystem` that rebuilds the PhysicsWorld at every predicting tick, and the character update uses that.
* We make sure to call `GhostPredictionSystemGroup.ShouldPredict` before running the character update. We must only update the character logic if we must predict.
* We make sure to use the player commands (`OnlineFPSPlayerCommands`) as inputs for the character movement.

Since this is a first-person game, the camera entity is a child of the character entity. It is controlled by calculating the desired horizontal and vertical angles, and applying them to the camera transform as a rotation.

Character rotation requires some special handling in this sample, for several different reasons:
* Instead of updating the character rotation in the usual character system that updates on a fixed time step, we update it in a separate system that updates at a variable time step: `OnlineFPSCharacterVariableUpdateSystem`. We want camera movement to be as smooth and responsive as possible, and updating it at a fixed time step, even if interpolated, would result in camera movement that isn't as good as it could be. 
* Since the character collider is a cylinder that is not 100% perfectly circular, rotating the character collider while standing next to walls could potentially result in small overlaps that would push us away from the wall. It is not necessarily a big problem, but this sample tries to demonstrate how to deal with this potential issue. Instead of rotating the root character object, we simply rotate a child object that represents the character meshes/visuals. This child object's rotation is calculated from the camera angles, since it must always be oriented towards the camera direction on the horizontal plane.

The actual rendered camera is a GameObject in the scene (named "GameObjectCamera"), but it constantly copies the transform of the entity marked as being the main camera entity. The GameObject camera registers itself as the rendering camera with the `RegisterAsMainCameraBehaviour` monobehaviour, and the `MainCameraSystem` handles setting its transform accordingly. 