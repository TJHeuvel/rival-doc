
# Scale Velocity With Slope Angle

You may want to make your character move slower uphill, and faster downhill. Here's one way of doing that:

Rival comes with a `CharacterControlUtilities.GetSlopeAngleTowardsDirection` function that calculates the signed slope angle in a given movement direction. The resulting angle will be positive if the slope goes up, and negative if the slope goes down.

Now you can simply apply a multiplier to your desired character velocity based on that signed slope angle. For example:
```cs
public void HandleCharacterControl()
{
    if (CharacterBody.IsGrounded)
    {
        // Move on ground
        float3 targetVelocity = MyCharacterInputs.WorldMoveVector * MyCharacter.GroundMaxSpeed;

        // Scale Velocity With Slope Angle
        if (math.lengthsq(targetVelocity) > 0f)
        {
            float slopeAngleDegrees = CharacterControlUtilities.GetSlopeAngleTowardsDirection(true, targetVelocity, CharacterBody.GroundHit.Normal, GroundingUp);
            if(slopeAngleDegrees > 0f)
            {
                // when going uphill,
                // reduce the target velocity to zero proportionally to the slope angle, and up to a maximum slope of 60 degrees
                float slopeRatio = 1f - math.clamp(slopeAngleDegrees / 60f, 0f, 1f);
                targetVelocity *= slopeRatio;
            } 
            else
            {
                // when going downhill,
                // increase the target velocity by up to 50% proportionally to the slope angle, and up to a maximum slope of 60 degrees
                float slopeRatio = math.clamp(slopeAngleDegrees / -60f, 0f, 1f);
                targetVelocity *= 1f + (0.5f * slopeRatio);
            }
        }

        CharacterControlUtilities.StandardGroundMove_Interpolated(ref CharacterBody.RelativeVelocity, targetVelocity, MyCharacter.GroundedMovementSharpness, DeltaTime, GroundingUp, CharacterBody.GroundHit.Normal);

        // (......)
    }
    // (......)
}

```