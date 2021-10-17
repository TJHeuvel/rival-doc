Back to [Tutorial](../tutorial.md)

# Tutorial - Double Jump

We will now add the ability to allow up to X additional while in air. 

We will start by adding an int field in `ThirdPersonCharacterComponent` to determine how many air jumps we are allowed to do, and we will also add a hidden int field to keep track of how many air jumps we've done so far.

```cs
[Serializable]
public struct ThirdPersonCharacterComponent : IComponentData
{
    // (...)

    public int MaxAirJumps;

    [UnityEngine.HideInInspector]
    public int CurrentAirJumps;
}
```

The rest of the implementation will be done in `ThirdPersonCharacterProcessor.HandleCharacterControl`. If we are not grounded and a jump is requested, we will check if we have reached our max in-air jumps count, and if not, we will jump. Also, when we are grounded, we always reset our `CurrentAirJumps` to 0.

```cs
public void HandleCharacterControl()
{
    if (CharacterBody.IsGrounded)
    {
        // (...)

        // Reset air jumps when grounded
        ThirdPersonCharacter.CurrentAirJumps = 0;
    }
    else
    {
        // Move in air
        // (...)

        // Air Jumps
        if (ThirdPersonCharacterInputs.JumpRequested && ThirdPersonCharacter.CurrentAirJumps < ThirdPersonCharacter.MaxAirJumps)
        {
            CharacterControlUtilities.StandardJump(ref CharacterBody, GroundingUp * ThirdPersonCharacter.JumpSpeed, true, GroundingUp);
            ThirdPersonCharacter.CurrentAirJumps++;
        }

        // Gravity
        // (...)
    }

    // (...)
}
```

Now if you set `MaxAirJumps` to 1 and press Play, you should be able to double jump. Additional air jumps can be performed with a value of more than 1.