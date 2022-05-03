
# Changelog

[Previous Versions](./Changelog/changelog-archive.md)

## v0.4.1

**Upgrade Tips:**
- If you use code that sets the character's `ParentEntity`, you must now also remember to set a `ParentAnchorPoint` (in `KinematicCharacterBody`)


**Changes:**
- update to Entities 0.50.1-preview.2 and Physics 0.50.0-preview 43
- Added a `ParentAnchorPoint` field to `KinematicCharacterBody`


**Fixes**
- Fixed selection of best ground hit when there are multiple candidates
- Fixed computation of momentum added by leaving a moving parent object



## v0.4

**Upgrade Tips:**
- The changes in this version are pretty significant and specific. For this reason, if you need to upgrade, you should consider perhaps using the updated standard characters as a starting point and re-copying your current character's implementation into them. It's not very ideal, but it could end up being less trouble than the alternative of trying to manually upgrade your existing character. Regardless, the necessary upgrade steps are described below
- All upgrade steps for DOTS 0.50 in general will apply to your project. [Please refer to this page for details](https://docs.unity3d.com/Packages/com.unity.entities@0.50/manual/upgrade-guide.html). The main points are: 
    - All systems must have the `partial` keyword
    - Systems that deal with physics should use the new functions for handling dependencies (`RegisterPhysicsRuntimeSystemReadWrite()`) in `OnStartRunning()`, instead of handling these manually
    - Remove the `ENABLE_HYBRID_RENDERER_V2` scripting defineeeeeee
- In order to fix a potential case of non-determinism, all Rival characters now have a new `StoredKinematicCharacterBodyProperties` component on them (automatically added through the `KinematicCharacterUtilities.HandleConversionForCharacter()` function). That component is used in some `KinematicCharacterUtilities` functions, so you must add a `[ReadOnly] public ComponentDataFromEntity<StoredKinematicCharacterBodyProperties>  StoredKinematicCharacterBodyPropertiesFromEntity;` to your existing character update jobs, and then pass it to your character processor, and plug it in the right places (let the error messages guide you). You can look for all places where `StoredKinematicCharacterBodyProperties` is used in the standard characters, and use that as a reference. This replaces what used to be a `ComponentDataFromEntity<KinematicCharacterBody>` in the character System and Processor
- A `StoreKinematicCharacterBodyPropertiesSystem` must now update directly before all Rival characters. This will work automatically if you use the `KinematicCharacterUpdateGroup` for your character update system, but if you're not using it, you might have to copy that system and add it to your own update loop before your character systems
- In the character body inspector, character interpolation will now be set to interpolate only translation by default; not rotation. You might need to activate rotation interpolation if you were handling rotation in a fixed step job 


**Changes:**
- Upgraded DOTS packages (Entities, Physics, Hybrid Renderer, Netcode) to 0.50, and removed DOTS Animation package
- The `CharacterInterpolation` component now has separate options for interpolating translation and rotation, which means there are now two separate checkboxes in the character body inspector for interpolation. By default, only translation interpolation is active, since the rotation of standard characters is updated at a variable rate now
- Physics dependency handling for character systems now use the new `RegisterPhysicsRuntimeSystemReadWrite()` approach
- Added a `FixedUpdateTickSystem` to the standard characters packages, to help deal with the complexities of input handling processed on fixed update. This system is used in the "PlayerSystems" of both standard characters
- Added a new `StoredKinematicCharacterBodyProperties` component to all Rival characters by default, along with a `StoreKinematicCharacterBodyPropertiesSystem`. This change was made to fix a potential case of non-determinism in Rival characters, and it will affect all existing characters. See "Upgrade Tips" section above for more info. In the character job, a `ComponentDataFromEntity<StoredKinematicCharacterBodyProperties>` now replaces what used to be a `ComponentDataFromEntity<KinematicCharacterBody>`
- Added a new [Standard Character Overview](./Tutorial/tutorial-standardcharacterexplanation) section to the Tutorial, where we explain in detail how the Standard Characters work
- [Standard Characters] Rotation of all standard characters (FirstPerson and ThirdPerson) is now handled in a separate variable-rate system
- [Platformer Sample] Replaced the now-incompatible DOTS animation package with a hybrid animation approach, using Mecanim. The DOTS Animation package [is still expected to be released one day](https://forum.unity.com/threads/dots-development-status-and-next-milestones-march-2022.1253355/page-2#post-8000859), but it will have to wait until after DOTS 1.0
- [OnlineFPS Sample] Large parts of the code were refactored in order to improve quality, clarity and correctness. Some changes were made as requirements for Netcode 0.50, while others were made simply to provide a better example of how to handle prediction


**Fixes:**
- Fixed a potential case of non-determinism in the standard characters, which could happen during character-to-character collisions. The non-determinism was due to reading character velocity of other characters in the same job that modifies the velocity of a character. The switch to using a `ComponentDataFromEntity<StoredKinematicCharacterBodyProperties>` is due to this


**Known Issues:**
- The [HideInInspector] attribute no longer seems to work, meaning some fields in authoring components that are meant to be hidden will now appear in the inspector. For example: the `FirstPersonPlayerAuthoring` component has a visible `Last Inputs Processing Tick` field that should normally not be visible
- [Samples] Environment reflections do not work. [This might be an issue with the hybrid renderer package](https://forum.unity.com/threads/environment-reflections-not-working-with-hybrid-renderer-0-50-0.1256121/)
- [Platformer Sample] The animation for "standing up from a ledge grab" is not properly implemented. The real implementation would require root motion, so it will be done at a later date when the DOTS Animation package is availabe once again and offers support for root motion
- [OnlineFPS Sample] The game is capped out at 110 fps for the time being. Otherwise, if the framerate becomes much higher than the tickrate of 60, there is [a spam of warnings in editor and in build that severely affect the framerate](https://forum.unity.com/threads/server-world-allocations-live-too-long-in-0-50-preview-29.1254225/) . This appears to be an issue with the Netcode package that should be solved in future versions
- [OnlineFPS Sample] Player input will fail to be processed on some frames, once in a while. This might be the cause of remote player rotations looking a bit jittery, and local player rotation feeling slightly off. It can also result in shoot/jump inputs failing to be processed sometimes. This appears to be an issue related to [remote player prediction in the Netcode package](https://forum.unity.com/threads/commands-issue-when-trying-to-use-remote-players-prediction.1259031/#post-7999692), but more investigation is needed in order to confirm