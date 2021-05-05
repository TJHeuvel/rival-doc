---
layout: default
title: Ignore Collisions Tag
parent: Tutorial
nav_order: 5
---

# Tutorial - Ignore Collisions Tag

We want to be able to make the character ignore all collisions with colliders that have a certain physics custom tag. We will add a box with a `PhysicsShape` and a `PhysicsBody` to the Subscene, and we will assign the custom tag '0' to the "Custom Tags" of its `PhysicsBody`:

![](../Images/tutorial_box_tag.png)

Next, we will add a field to our `TutorialCharacterComponent` so we can specify which tag we want to ignore. You will have to add `using Unity.Physics.Authoring;` at the top of the file for this to work. Look for the `// here` comment:

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
    public int MaxAirJumps; 
    public CustomPhysicsBodyTags IgnoredPhysicsTags; // here

    [UnityEngine.HideInInspector]
    public int _currentAirJumps; 
}
```

Then, we will go to `TutorialCharacterProcessor` and add a field of type `TutorialCharacterComponent`. This is so that our processor has access to the ignored physics tag that we defined in that component. And then, we will modify `TutorialCharacterProcessor.CanCollideWithHit` so that it also ignores collisions with colliders that have that physics tag. Look for the `// here` comments:

```cs
public struct TutorialCharacterProcessor : IKinematicCharacterProcessor
{
    public TutorialCharacterComponent TutorialCharacter; // here

    public bool CanCollideWithHit(
        ref KinematicCharacterCommonData commonData,
        in BasicHit hit,
        Entity characterEntity)
    {
        // here (the entire function)

        // First, see if we'd have to ignore based on the default implementation
        if (!KinematicCharacterUtilities.DefaultMethods.CanCollideWithHit(in hit))
        {
            return false;
        }

        // if not, check for the ignored tag
        if (TutorialCharacter.IgnoredPhysicsTags.Value > CustomPhysicsBodyTags.Nothing.Value)
        {
            if ((commonData.CollisionWorld.Bodies[hit.RigidBodyIndex].CustomTags & TutorialCharacter.IgnoredPhysicsTags.Value) > 0)
            {
                return false;
            }
        }

        return true;
    }

    // (........)
}
```

The last step is to make sure the `TutorialCharacter` field we just added to our `TutorialCharacterProcessor` is properly assigned. We will go to `TutorialCharacterSystem`, and when we create the processor in our `TutorialCharacterUpdateData` struct, we will assign the proper value to that field. Look for the `// here` comment:

```cs
for (int i = 0; i < chunk.Count; i++)
{
    Entity entity = chunkEntities[i];

    // Build a data structure containing all that we might need during our update
    TutorialCharacterUpdateData d = new TutorialCharacterUpdateData
    {
        Processor = new TutorialCharacterProcessor
        {
            TutorialCharacter = chunkTutorialCharacters[i], // here
        },
        CommonData = tmpCommonData,

        Entity = entity,
        Translation = chunkTranslations[i].Value,
        Rotation = chunkRotations[i].Value,
        PhysicsCollider = chunkPhysicsColliders[i],
        CharacterBody = CharacterBodyFromEntity[entity],
        CharacterHitsBuffer = chunkCharacterHitBuffers[i],
        CharacterDeferredImpulsesBuffer = chunkCharacterDeferredImpulsesBuffers[i],
        VelocityProjectionHitsBuffer = chunkVelocityProjectionHitBuffers[i],
        StatefulCharacterHitsBuffer = chunkStatefulCharacterHitsBuffers[i],

        TutorialCharacter = chunkTutorialCharacters[i],
        CharacterInputs = chunkTutorialCharacterInputs[i],

        // Add & set-up any extra data or ComponentDataFromEntitys you might need in your update here
    };

    // (.................................)
```

You can set the proper ignored tag in your character authoring's inspector, press Play, and try to collide with the box marked with the ignored tag. You should go right through. 

Note 1: if we wanted to, we could ignore collisions based on the presence of a component instead of on a physics tag. All we would have to do is that instead of giving a `CustomPhysicsBodyTags` to our `TutorialCharacterProcessor`, we would give it a `ComponentDataFromEntity<MyCharacterIgnoreComponent>`. And instead of checking for the presence of the tag in `TutorialCharacterProcessor.CanCollideWithHit`, we would check for the presence of the component, by using the `ComponentDataFromEntity<MyCharacterIgnoreComponent>`.

Note 2: since the callbacks of the `TutorialCharacterProcessor` can potentially be called multiple times per character per frame, it is always a good idea to try to make their logic as inexpensive as possible. If you can, it is still better to ignore collisions with physics categories instead of by checking for a tag or component.

![](../Images/tutorial_ignore_collisions.gif)