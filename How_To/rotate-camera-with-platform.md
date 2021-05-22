

# Rotate Camera With Moving Platform

When your character is standing on a moving platform, you may want your camera to rotate along with the character.

In order to accomplish this, you have to calculate by how much the moving platform rotated the character on a given frame, and rotate the camera by the same amount. One important thing to note, however, is that in most games, the camera will update on variable update while the character will update on fixed update. That means we can't simply make the character remember by how much it got rotated during its update and then add that rotation to the camera, because we would be applying a rotation that happened over a different period of time. 

Instead, here's what we could do:
* The camera keeps track of its followed character's `ParentEntity` is every frame (moving platforms are automatically assigned as the character's `ParentEntity` when the characer becomes grounded on them). It remembers a `public Entity PreviousCharacterParent;` and a `public quaternion PreviousCharacterParentRotation;`.
* When the camera remembers the `PreviousCharacterParentRotation`, it makes sure to save the **interpolated** rotation of the moving platform. That means we need to use the `LocalToWorld` component of the moving platform entity for rotation; not the `Rotation` component.
* In the camera's update, call `KinematicCharacterUtilities.ApplyRotationDeltaFromParentMovement` to calculate the rotation that the moving platform would've cause on the character on this frame, and apply that rotation to the camera's rotation.

Here is sample code that you could have somewhere in your camera update:
```cs
// (...)

// Make the camera rotate along with the character's moving platform
if (characterParentEntity != Entity.Null)
{
    // Get the current character parent interpolated rotation
    LocalToWorld parentLocalToWorld = GetComponent<LocalToWorld>(characterParentEntity);
    quaternion characterParentRotation = quaternion.LookRotationSafe(parentLocalToWorld.Forward, parentLocalToWorld.Up); 

    if (characterParentEntity == myCamera.PreviousParentEntity)
    {
        // Calculate & apply the moving platform rotation to the camera
        KinematicCharacterUtilities.ApplyRotationDeltaFromParentMovement(ref cameraRotation.Value, myCamera.PreviousParentRotation, characterParentRotation, characterGroundingUp, true);
    }

    // Remember parent values for next frame
    myCamera.PreviousParentRotation = characterParentRotation;
    myCamera.PreviousParentEntity = characterParentEntity;
}
```

___________________________

Alternatively, you could also make the character component remember by how much it got rotated during the `KinematicCharacterUtilities.ParentMovementUpdate` of its processor's `OnUpdate`, and then make the camera rotate by a fraction of that rotation corresponding to `(deltaTime / fixedDeltaTime) * characterRotationDelta`