
[Tutorial](../tutorial)

# Tutorial - Input Handling

Now we must write code to control our character. For the sake of simplicity, the camera will be stationary and the input handling will be very rudimentary. Create a new ECS system called `TutorialInputSystem` that looks like this (all explanations are in the comments):

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
public class TutorialInputSystem : SystemBase
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
            }).Schedule();
    }
}
```

If you press Play now, your character should be controllable using WASD to move, and Space to jump. You can play with the parameters under the `TutorialCharacterAuthoring` component add more colliders to your Subscene (walls, ramps, dynamic bodies, etc...) to try things out.

For more information on the fixed-step input handling problem described in the comments over the jump handling, see [Input Handling](../How_to/input-handling).

In a real project, you will surely want to handle input in a more complex way that will allow you to only apply inputs to a specific player-controlled character Entity, instead of applying input to ALL characters in the scene. The camera transform could also be represented by an Entity instead of just the GameObject camera. But since these things can be highly-specific to your game, Rival does not try to impose any particular way of handling them. You can look at the various sample projects in order to understand how this could be done.
