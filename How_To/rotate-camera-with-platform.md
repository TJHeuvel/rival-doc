Back to [How To](../how-to.md)

# Rotate Camera With Moving Platform

When your character is standing on a moving platform, you may want your camera to be rotated along with the character.

In order to accomplish this, we have to find out by how much the moving platform movement rotated the character over the last update, and apply that same rotation to the camera. `KinematicCharacterBody.RotationFromParent` already contains that information. (Reminder: the internal logic for handling moving platforms in Rival simply assigns any valid ground entity as the `KinematicCharacterBody.ParentEntity`. So the moving platform is the "Parent" in this case).

However, we have a problem! The character gets rotated by moving platforms on a fixed timestep, but a game gamera will typically update on a variable timestep. So the real rotation we must use in order to rotate our camera is the portion of `KinematicCharacterBody.RotationFromParent` that represents the `deltaTime / fixedDeltaTime` ratio. In other words, if our character rotated by 10 degrees over a fixedDeltaTime of 0.1 seconds, a camera whose deltaTime on this frame is 0.05 must rotate by "(0.05 / 0.1) * 10" = 5 degrees on that frame.

Here is sample code demonstrating how you could implement this in your own camera system:
```cs
// Camera update

// (.... your own code here )

// Handle rotating the camera along with character's parent entity (moving platform)
if (HasComponent<KinematicCharacterBody>(myCamera.FollowedCharacter))
{
    // Get the character body component on the character that the camera follows
    KinematicCharacterBody characterBody = GetComponent<KinematicCharacterBody>(myCamera.FollowedCharacter);

    // Calculate the deltaTime ratio compared to the fixedDeltaTime
    float rotationRatio = math.clamp(deltaTime / fixedDeltaTime, 0f, 1f);

    // Get the portion of the "RotationFromParent" corresponding to the previously calculated "rotationRatio"
    quaternion rotationFromCharacterParent = math.slerp(quaternion.identity, characterBody.RotationFromParent, rotationRatio);

    // Make our camera rotate by that rotation
    cameraRotation.Value = math.mul(cameraRotation.Value, rotationFromCharacterParent);
}

// (.... your own code here )

```

Note: `fixedDeltaTime` can be obtained like this:
```cs
float fixedDeltaTime = World.GetOrCreateSystem<FixedStepSimulationSystemGroup>().FixedRateManager.Timestep;
```