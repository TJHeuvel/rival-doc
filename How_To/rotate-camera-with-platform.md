Back to [How To](../how-to.md)

# Rotate Camera With Moving Platform

When your character is standing on a moving platform, you may want your camera to be rotated along with the character.

In order to accomplish this, we have to find out by how much the moving platform movement rotated the character over the last update, and apply that same rotation to the camera. `KinematicCharacterBody.RotationFromParent` already contains that information. (Reminder: the internal logic for handling moving platforms in Rival simply assigns any valid ground entity as the `KinematicCharacterBody.ParentEntity`. So the moving platform is the "Parent" in this case).

However, we have a problem! The character gets rotated by moving platforms on a fixed timestep, but a game camera will typically update on a variable timestep. So the real rotation we must use in order to rotate our camera is the portion of `KinematicCharacterBody.RotationFromParent` that represents the `deltaTime / fixedDeltaTime` ratio. In other words, if our character rotated by 10 degrees over a fixedDeltaTime of 0.1 seconds, a camera whose deltaTime on this frame is 0.05 must rotate by "(0.05 / 0.1) * 10" = 5 degrees on that frame.

Rival comes with a pre-made utility function that calculates all this: `KinematicCharacterUtilities.ApplyParentRotationToTargetRotation`. Simply call this in your camera update, and give it the rotation of the entity you wish to rotate (in this case; the camera), along with the `KinematicCharacterBody` of the character it follows, the fixedDeltaTime, and the deltaTime. It will modify the camera's rotation accordingly.

Note: `fixedDeltaTime` can be obtained like this:
```cs
float fixedDeltaTime = World.GetOrCreateSystem<FixedStepSimulationSystemGroup>().FixedRateManager.Timestep;
```

Note: The `OrbitCameraSystem` in the standard third person character shows an example of this