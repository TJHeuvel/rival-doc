Back to [How To](../how-to.md)

# Frame-Perfect Rotation
The default code-generated character update job, which updates at a fixed timestep, may not always be the ideal place to handle character rotation. It's fine when the character rotates independantly from the camera, but in games like FPSs or over-the-shoulder shooters where the character rotation & camera must be synced 1:1, you may start seeing a delay between the camera rotation and the character rotation. This is due to interpolation 

In those cases, here is what you should do:
- Get rid of all rotation handling in the `MyCharacterJob` and `MyCharacterProcessor`. 
    - Remove the line at the end of the `MyCharacterJob` where we write back the rotation to the chunk
    - remove rotation handling in your `MyCharacterProcessor.HandleCharacterMovement`
    - remove the line where we modify `Rotation` right after the call to `KinematicCharacterUtilities.ParentMovementUpdate` in your `MyCharacterProcessor.OnUpdate`
- Make a new system that updates in `SimulationSystemGroup`, where you will handle all rotation for your character.
- In your rotation update, call `characterInterpolation.SkipNextRotationInterpolation();` on the `CharacterInterpolation` component present on the character entity (only necessary if you have Character Interpolation enabled)
- In your rotation update, call `KinematicCharacterUtilities.ApplyParentRotationToTargetRotation` in order to handle making your character rotate along with its parent entity

The `FirstPersonCharacterRotationSystem`, in the StandardCharacters package, shows an example of this.

This will make your character rotate on regular update instead of fixed update, and therefore will get rid of any delay between camera rotation & character rotation