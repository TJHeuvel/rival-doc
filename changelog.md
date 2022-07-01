
# Changelog

[Previous Versions](./Changelog/changelog-archive.md)


## v0.5.0

**Upgrade Tips:**
- The parameters of the `OverrideDynamicHitMasses` processor callback have changed. Keep in mind that you'll now be working with "inverse masses" (1 / mass) instead of masses. The implemented interface function in your processor should now look like this:
```cs
public void OverrideDynamicHitMasses(
    ref PhysicsMass characterMass,
    ref PhysicsMass otherMass,
    Entity characterEntity,
    Entity otherEntity,
    int otherRigidbodyIndex)
{
}
```
- The parameters of the `KinematicCharacterUtilities.DefaultMethods.UpdateGroundPushing` function have changed. In the standard characters, the function call should now look like this:
```cs
KinematicCharacterUtilities.DefaultMethods.UpdateGroundPushing(ref this, ref CharacterDeferredImpulsesBuffer, ref CharacterBody, DeltaTime, Entity, ThirdPersonCharacter.Gravity, Translation, Rotation, 1f);
```


**Changes:**
- update to Entities 0.51 and Unity 2021.3
- Added a "Limitations" page for the documentation website
- The processor's `OverrideDynamicHitMasses` now deals with `PhysicsMasses` instead of masses. This is because working with "inverse masses" makes it easier to represent infinite mass ratios, and it prevents a divide-by-0 pitfall. `PhysicsMass` also lets you change moment of inertia (the "rotational mass").
- Removed the `otherEntityIsCharacter` parameter of the processor's `OverrideDynamicHitMasses`. `OverrideDynamicHitMasses` cannot work when two dynamic characters are trying to push each-other, and so the function will simply not be called in that case
- In `KinematicCharacterBody`, `AddCloseHitsForVelocityProjection` was renamed to `ProjectVelocityOnInitialOverlaps` for more clarity
- `KinematicCharacterDeferredImpulse` now works with direct velocity changes rather than impulses
- Added a `KinematicCharacterUtilities.SetOrUpdateParentBody()` that takes care of properly setting parent data in the characterBody


**Fixes**
- Improved the correctness of rigidbody pushing when enabling `SimulateDynamicBody` on the character
- `KinematicCharacterUtilities.DefaultMethods.UpdateGroundPushing` now properly takes into account masses overriden by `OverrideDynamicHitMasses`
- Fixed jitter when a kinematic character was pushing a dynamic rigidbody
- fixed velocity projection issue that could result in our character staying stuck on the edge of a rolling sphere 