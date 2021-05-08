---
layout: default
title: Friction Surface
parent: Tutorial
nav_order: 6
---

# Tutorial - Friction Surface

We want to be able to create surfaces where the character has a limited movement speed. To do this, we will first create a new `CharacterFrictionSurface` component:

```cs
[Serializable]
[GenerateAuthoringComponent]
public struct CharacterFrictionSurface : IComponentData
{
    public float VelocityFactor;
}
```

Then, we need to make that component accessible from our `TutorialCharacterProcessor`. Here's how we can pass extra data to our processor:
- Add a `public ComponentDataFromEntity<CharacterFrictionSurface> CharacterFrictionSurfaceFromEntity;` field to the `TutorialCharacterProcessor` struct
- Add a `[ReadOnly] public ComponentDataFromEntity<CharacterFrictionSurface> CharacterFrictionSurfaceFromEntity;` field to the `TutorialCharacterJob` struct
- Make `TutorialCharacterSystem` pass that `ComponentDataFromEntity` to the `TutorialCharacterJob`
- Make `TutorialCharacterJob` pass that `ComponentDataFromEntity` to the `TutorialCharacterProcessor`

In short, when we need to add extra data access in `TutorialCharacterProcessor`, we make our `TutorialCharacterSystem` pass that data to the `TutorialCharacterJob`, which in turn passes that data to the `TutorialCharacterProcessor` in its update loop.

```cs
[UpdateInGroup(typeof(KinematicCharacterUpdateGroup))]
public class TutorialCharacterSystem : SystemBase
{
    [BurstCompile]
    public struct TutorialCharacterJob : IJobEntityBatchWithIndex
    {
        // (...)

        [ReadOnly]
        public ComponentDataFromEntity<CharacterFrictionSurface> CharacterFrictionSurfaceFromEntity;

        // (...)

        public void Execute(ArchetypeChunk chunk, int batchIndex, int indexOfFirstEntityInQuery)
        {
            // (...)

            TutorialCharacterProcessor processor = default;
            // (...)
            processor.CharacterFrictionSurfaceFromEntity = CharacterFrictionSurfaceFromEntity;

            // (...)

            // Iterate on individual characters
            for (int i = 0; i < chunk.Count; i++)
            {
                // (...)
            }
        }
    }
    
    // (...)

    protected unsafe override void OnUpdate()
    {
        // (...)

        Dependency = new TutorialCharacterJob
        {
            // (...)

            CharacterFrictionSurfaceFromEntity = GetComponentDataFromEntity<CharacterFrictionSurface>(true),

            // (...)
        }.ScheduleParallel(CharacterQuery, 1, Dependency);

        // (...)
    }
}

```

At this point, we are ready to use the `CharacterFrictionSurfaceFromEntity` in our character movement implementation in `TutorialCharacterProcessor.HandleCharacterControl`. Look for the `// here` comment:

```cs
public void HandleCharacterControl()
{
    if (CharacterBody.IsGrounded)
    {
        // Move on ground
        // (...)

        // Handle Sprint
        // (...)

        // Modify final velocity based on friction surface
        if (CharacterFrictionSurfaceFromEntity.HasComponent(CharacterBody.GroundHit.Entity))
        {
            targetVelocity *= CharacterFrictionSurfaceFromEntity[CharacterBody.GroundHit.Entity].VelocityFactor;
        }

        // (...)
    }
    else
    {
        // (...)
    }
    
    // (...)
}
```

Finally, you can add a new box object to the Subscene, with a Box `PhysicsShape`, and a `CharacterFrictionSurface` component. If you set the `VelocityFactor` to something like 0.2 on that `CharacterFrictionSurface` and you press Play, you should see your character move much slower on that surface.

![](../Images/tutorial_friction_surface.gif)