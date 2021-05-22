
[Tutorial](../tutorial.md)

# Tutorial - Sprint

We will add a Sprint functionality to the character. When pressing the LeftShift, we want to apply a multiplier to our character velocity.

First, we will modify the `TutorialCharacterInputs` component to add a field for Sprint input.

```cs
[Serializable]
public struct TutorialCharacterInputs : IComponentData
{
    // (...)
    public bool Sprint; 
}
```

Then we will add a `SprintSpeedMultiplier` field to our `TutorialCharacterComponent`.

```cs
[Serializable]
public struct TutorialCharacterComponent : IComponentData
{
    // (...)
    public float SprintSpeedMultiplier; 
}
```

Then, we will modify our `PlayerSystem` to gather sprint input and set it into that component. Before the `Entities.ForEach`, we will gather input with `bool sprintInput = Input.GetKey(KeyCode.LeftShift);`, and inside of the `Entities.ForEach`, we will apply it like this: `characterInputs.Sprint = sprintInput;`.

```cs
public class PlayerSystem : SystemBase
{
    // (...)

    protected override void OnUpdate()
    {
        // (...)

        // Gather raw input
        // (...)
        bool sprintInput = Input.GetKey(KeyCode.LeftShift);

        // Iterate on all Player components to apply input to their character
        Entities
            .ForEach((ref Player player) =>
            {
                // Character control
                if (HasComponent<TutorialCharacterInputs>(player.ControlledCharacter))
                {
                    // (...)
                    
                    characterInputs.Sprint = sprintInput;

                    // (...)
                }

                // Camera control
                // (...)
            }).Schedule();
    }
}
```

Finally, we have to modify the way our character handles velocity in `TutorialCharacterProcessor.HandleCharacterControl`. We want to make it so that when we are grounded, if `Sprint` is true in `TutorialCharacterInputs`, we apply a multiplier to our `targetVelocity`.

```cs

    public void HandleCharacterControl()
    {
        if (CharacterBody.IsGrounded)
        {
            // Move on ground
            float3 targetVelocity = TutorialCharacterInputs.WorldMoveVector * TutorialCharacter.GroundMaxSpeed;

            // Handle Sprint
            if (TutorialCharacterInputs.Sprint)
            {
                targetVelocity *= TutorialCharacter.SprintSpeedMultiplier;
            }

            CharacterControlUtilities.StandardGroundMove_Interpolated(ref CharacterBody.RelativeVelocity, targetVelocity, TutorialCharacter.GroundedMovementSharpness, DeltaTime, GroundingUp, CharacterBody.GroundHit.Normal);

        // (...)
}
```

Now, we can set a value of "2" to this `SprintSpeedMultiplier` in the inspector, press Play, and try it out.
