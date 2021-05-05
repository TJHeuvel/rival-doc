---
layout: default
title: Double Jump
parent: Tutorial
nav_order: 3
---

# Tutorial - Double Jump

We want to add the ability to allow up to X additional while in air. We will start by adding an int field in `TutorialCharacterComponent` to determine how many air jumps we are allowed to do, and we will also add a hidden int field to keep track of how many air jumps we've done so far. Look for the `// here` comments:

```cs
[Serializable]
public struct TutorialCharacterComponent : IComponentData
{
    public float RotationSharpness;
    public float GroundMaxSpeed;
    public float GroundedMovementSharpness;
    public float AirAcceleration;
    public float AirMaxSpeed;
    public float AirDrag;
    public float JumpSpeed;
    public float3 Gravity;

    public float SprintSpeedMultiplier;
    public int MaxAirJumps; // here

    [UnityEngine.HideInInspector]
    public int _currentAirJumps; // here
}
```

The rest of the implementation will be done in `TutorialCharacterImplementation.BeforeCharacterMove`. If we are not grounded and a jump is requested, we will check if we have reached our max in-air jumps count, and if not, we will jump. Also, when we are grounded, we always reset our `_currentAirJumps` to 0. Look for the `// here` comments:

```cs
public static void BeforeCharacterMove(ref TutorialCharacterUpdateData d)
{
    if (d.CharacterBody.IsGrounded)
    {
        // Move on ground
        float3 targetVelocity = d.CharacterInputs.WorldMoveVector * d.TutorialCharacter.GroundMaxSpeed;

        if(d.CharacterInputs.Sprint)
        {
            targetVelocity *= d.TutorialCharacter.SprintSpeedMultiplier;
        }

        CharacterControlUtilities.StandardGroundMove_Interpolated(ref d.CharacterBody.RelativeVelocity, targetVelocity, d.TutorialCharacter.GroundedMovementSharpness, d.CommonData.DeltaTime, d.CharacterUp, d.CharacterBody.GroundHit.Normal);

        // Jump
        if (d.CharacterInputs.JumpRequested)
        {
            CharacterControlUtilities.StandardJump(ref d.CharacterBody, d.CharacterUp * d.TutorialCharacter.JumpSpeed, true, d.CharacterUp);
        }

        // Push ground dynamic bodies
        KinematicCharacterUtilities.DefaultMethods.UpdateGroundPushing(ref d.CommonData, ref d.CharacterDeferredImpulsesBuffer, ref d.CharacterBody, d.TutorialCharacter.Gravity, 1f);

        // here
        // Reset air jumps when grounded
        d.TutorialCharacter._currentAirJumps = 0;
    }
    else
    {
        // Move in air
        float3 airAcceleration = d.CharacterInputs.WorldMoveVector * d.TutorialCharacter.AirAcceleration;
        CharacterControlUtilities.StandardAirMove(ref d.CharacterBody.RelativeVelocity, airAcceleration, d.TutorialCharacter.AirMaxSpeed, d.CharacterUp, d.CommonData.DeltaTime, false);

        // here
        // Air Jumps
        if (d.CharacterInputs.JumpRequested && d.TutorialCharacter._currentAirJumps < d.TutorialCharacter.MaxAirJumps)
        {
            CharacterControlUtilities.StandardJump(ref d.CharacterBody, d.CharacterUp * d.TutorialCharacter.JumpSpeed, true, d.CharacterUp);
            d.TutorialCharacter._currentAirJumps++;
        }

        // Gravity
        CharacterControlUtilities.AccelerateVelocity(ref d.CharacterBody.RelativeVelocity, d.TutorialCharacter.Gravity, d.CommonData.DeltaTime);

        // Drag
        CharacterControlUtilities.ApplyDragToVelocity(ref d.CharacterBody.RelativeVelocity, d.CommonData.DeltaTime, d.TutorialCharacter.AirDrag);
    }

    // Rotation (towards move direction)
    if (math.lengthsq(d.CharacterInputs.WorldMoveVector) > 0f)
    {
        CharacterControlUtilities.SlerpRotationTowardsDirectionAroundUp(ref d.Rotation, d.CommonData.DeltaTime, math.normalizesafe(d.CharacterInputs.WorldMoveVector), d.CharacterUp, d.TutorialCharacter.RotationSharpness);
    }

    // Reset jump request
    d.CharacterInputs.JumpRequested = false;
}
```