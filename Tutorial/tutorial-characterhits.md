
Back to [Tutorial](../tutorial.md)

# Tutorial - Character Hits

Now we will look at how we can iterate on character hits. 

We will create a new system that updates after the `KinematicCharacterUpdateGroup`, and iterates on all `DynamicBuffer<KinematicCharacterHit>`. During the character update, all hits that have been detected are added to this buffer. Therefore we must iterate on it *after* the entire character update is finished if we want all detected hits to be present.

We will also create a second job iterating on `DynamicBuffer<StatefulKinematicCharacterHit>`. These hits are similar to `KinematicCharacterHit`, except they also keep track of the state of the hit (Enter, Exit, Stay).

```cs
using Unity.Burst;
using Unity.Collections;
using Unity.Entities;
using Unity.Jobs;
using Unity.Mathematics;
using Unity.Transforms;
using Rival;
 
// Update in the fixed step group, and AFTER the character update
[UpdateInGroup(typeof(FixedStepSimulationSystemGroup))]
[UpdateAfter(typeof(KinematicCharacterUpdateGroup))]
public partial class CharacterHitsDetectionSystem : SystemBase
{
    protected override void OnUpdate()
    {
        // Iterate on non-stateful hits
        Entities
            .ForEach((Entity entity, ref DynamicBuffer<KinematicCharacterHit> characterHitsBuffer) => 
            {
                for (int i = 0; i < characterHitsBuffer.Length; i++)
                {
                    KinematicCharacterHit hit = characterHitsBuffer[i];
                    if(!hit.IsGroundedOnHit)
                    {
                        UnityEngine.Debug.Log("Detected an ungrounded hit");
                    }
                }
            }).Run();

        // Iterate on stateful hits
        Entities
            .ForEach((Entity entity, ref DynamicBuffer<StatefulKinematicCharacterHit> statefulCharacterHitsBuffer) =>
            {
                for (int i = 0; i < statefulCharacterHitsBuffer.Length; i++)
                {
                    StatefulKinematicCharacterHit hit = statefulCharacterHitsBuffer[i];
                    if (hit.State == CharacterHitState.Enter)
                    {
                        UnityEngine.Debug.Log("Entered new hit");
                    }
                }
            }).Run();
    }
}
```

This system demonstrates how to iterate over those character hits. In this example, the system will print a message in the console whenever the character detected an ungrounded hit, and every time we have entered any new hit