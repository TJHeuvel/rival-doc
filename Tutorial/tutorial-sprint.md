---
layout: default
title: Sprint
parent: Tutorial
nav_order: 2
---

# Tutorial - Sprint

We will add a Sprint functionality to the character. When pressing the LeftShift, we want to apply a multiplier to our character velocity.

First, we will modify the `TutorialCharacterInputs` component to add a field for Sprint input. Look for the `// here` comment

```cs
[Serializable]
public struct TutorialCharacterInputs : IComponentData
{
    public float3 WorldMoveVector;
    public bool JumpRequested;
    public bool Sprint; // here
}
```

Then we will add a `SprintSpeedMultiplier` field to our `TutorialCharacterComponent`. Look for the `// here` comment

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

    public float SprintSpeedMultiplier; // here
}
```

Then, we will modify our `CharacterInputSystem` to gather sprint input and set it into that component. Before the `Entities.ForEach`, we will gather input with `bool sprintInput = Input.GetKey(KeyCode.LeftShift);`, and inside of the `Entities.ForEach`, we will apply it like this: `characterInputs.Sprint = sprintInput;`. Look for the `// here` comments:

```cs
using Unity.Burst;
using Unity.Collections;
using Unity.Entities;
using Unity.Jobs;
using Unity.Mathematics;
using Unity.Transforms;
using UnityEngine;

// Make the input system update on variable update, and before the FixedStepSimulationSystemGroup (where the character updates)
[UpdateInGroup(typeof(SimulationSystemGroup), OrderFirst = true)]
[UpdateBefore(typeof(FixedStepSimulationSystemGroup))]
public class CharacterInputSystem : SystemBase
{
    protected override void OnUpdate()
    {
        // Remember camera directions (since that will be used as a movement direction for the character)
        quaternion cameraRotation = Camera.main.transform.rotation;
        float3 cameraForwardOnUpPlane = math.normalizesafe(Rival.MathUtilities.ProjectOnPlane(Rival.MathUtilities.GetForwardFromRotation(cameraRotation), math.up()));
        float3 cameraRight = Rival.MathUtilities.GetRightFromRotation(cameraRotation);

        // Gather raw input
        float2 moveInput = float2.zero;
        moveInput.y += Input.GetKey(KeyCode.W) ? 1f : 0f;
        moveInput.y += Input.GetKey(KeyCode.S) ? -1f : 0f;
        moveInput.x += Input.GetKey(KeyCode.D) ? 1f : 0f;
        moveInput.x += Input.GetKey(KeyCode.A) ? -1f : 0f;
        bool jumpInput = Input.GetKeyDown(KeyCode.Space);
        bool sprintInput = Input.GetKey(KeyCode.LeftShift); // here

        // Iterate on all TutorialCharacterInputs components to apply input to them
        Entities
            .ForEach((ref TutorialCharacterInputs characterInputs) =>
            {
                // Make our WorldMoveVector be a vector of magnitude 0 to 1, pointing in the direction of the desired movement (towards camera forward, in this case).
                // The magnitude of this vector represents the fraction of maximum character speed we wish to have in this direction,
                // so a magnitude of 1f will correspond to the max velocity, 0.5f will correspond to half of the max velocity, etc...
                characterInputs.WorldMoveVector = (moveInput.y * cameraForwardOnUpPlane) + (moveInput.x * cameraRight);
                characterInputs.WorldMoveVector = Rival.MathUtilities.ClampToMaxLength(characterInputs.WorldMoveVector, 1f);

                // Remember we want to jump, but only if the jump input is true. The character update will handle resetting that value to false every frame.
                // Since our character updates on a fixed step and our input is gathered at a variable step, this setup is necessary to prevent having your jump
                // input lost between two fixed updates.
                if (jumpInput)
                {
                    characterInputs.JumpRequested = jumpInput;
                }

                characterInputs.Sprint = sprintInput; // here
            }).Schedule();
    }
}
```

Finally, we have to modify the way our character handles velocity in `TutorialCharacterImplementation.BeforeCharacterMove`. We want to make it so that when we are grounded, if `Sprint` is true in `TutorialCharacterInputs`, we apply a multiplier to our `targetVelocity`. Look for the `// here` comment:

```cs
public static void BeforeCharacterMove(ref TutorialCharacterUpdateData d)
{
    if (d.CharacterBody.IsGrounded)
    {
        // Move on ground
        float3 targetVelocity = d.CharacterInputs.WorldMoveVector * d.TutorialCharacter.GroundMaxSpeed;

        // here
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
    }
    else
    {
        // Move in air
        float3 airAcceleration = d.CharacterInputs.WorldMoveVector * d.TutorialCharacter.AirAcceleration;
        CharacterControlUtilities.StandardAirMove(ref d.CharacterBody.RelativeVelocity, airAcceleration, d.TutorialCharacter.AirMaxSpeed, d.CharacterUp, d.CommonData.DeltaTime, false);

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

Note: the `TutorialCharacterUpdateData` is the struct that is filled by the `TutorialCharacterSystem` with all of the data that our character update might need during the update. It already contains the `TutorialCharacterInputs` (`d.CharacterInputs`) and the `TutorialCharacterComponent` (`d.TutorialCharacter`).

Now, we can set a value of "2" to this `SprintSpeedMultiplier` in the inspector, press Play, and try it out.
