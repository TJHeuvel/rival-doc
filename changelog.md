
# Changelog

[Previous Versions](./Changelog/changelog-archive.md)


## v0.4

**Upgrade Tips:**
- The changes in this version are pretty significant and specific. For this reason, if you need to upgrade, you should consider perhaps using the updated standard characters as a starting point and re-copying your current character's implementation into them. It's not very ideal, but it could end up being less trouble than the alternative of trying to manually upgrade your existing character. The necessary upgrade steps are described below
- All upgrade steps for DOTS 0.50 in general will apply to your project. Please refer to this page for details: https://docs.unity3d.com/Packages/com.unity.entities@0.50/manual/upgrade-guide.html. The main points are: 
    - All systems must be preceded by the `partial` keyword
    - Systems that deal with physics shuould use the new functions for handling dependencies (`RegisterPhysicsRuntimeSystemReadWrite()`) in `OnStartRunning()`, instead of handling these manually
    - Remove the `ENABLE_HYBRID_RENDERER_V2` scripting define
- All Rival characters now have a new `StoredKinematicCharacterBodyProperties` component on them (automatically added through the `KinematicCharacterUtilities.HandleConversionForCharacter()` function). That component is used in some `KinematicCharacterUtilities` functions, and so you must add a `[ReadOnly] public ComponentDataFromEntity<StoredKinematicCharacterBodyProperties> StoredKinematicCharacterBodyPropertiesFromEntity;` to your existing character update jobs, and then pass it to your character processor, and plug it in the right places (let the error messages guide you). You can look for all places where `StoredKinematicCharacterBodyProperties` is used in the standard characters, and copy that.
- A `StoreKinematicCharacterBodyPropertiesSystem` must now update directly before all Rival characters. This will work automatically if you use the `KinematicCharacterUpdateGroup` for your character update system, but if you're not using it, you might have to copy that system and add it to your own update loop before your character systems


**Changes:**
- Upgraded DOTS packages (Entities, Physics, Hybrid Renderer, Netcode) to 0.50
- Physics dependency handling for character systems now use the new `RegisterPhysicsRuntimeSystemReadWrite()` approach
- Added a `FixedUpdateTickSystem` to the standard characters packages, to help deal with the complexities of input handling processed on fixed update. This system is used in the "PlayerSystems" of both standard characters
- Added a new `StoredKinematicCharacterBodyProperties` component to all Rival characters by default, along with a `StoreKinematicCharacterBodyPropertiesSystem`. This change was made to fix a potential case of non-determinism in Rival characters, and it will affect all existing characters. See "Upgrade Tips" section above for more info
- [Platformer Sample] Replaced deprecated DOTS animation solution with a hybrid approach, using Mecanim
- [OnlineFPS Sample] Most of the code was refactored in order to improve quality, clarity and correctness. Some other changes were made as requirements for Netcode 0.50


**Fixes:**
- Fixed a potential case of non-determinism in the standard characters, which could happen during character-to-character collisions


**Known Issues:**
- [Samples] Environment reflections do not work. This might be an issue with the hybrid renderer package
- [Platformer Sample] The animation for "standing up from a ledge grab" is not properly implemented. The real implementation would require root motion, so it will be done at a later date when the DOTS Animation package is availabe once again and offers support for root motion
- [OnlineFPS Sample] The game is capped out at 110 fps for the time being. Otherwise, there is a spam of warnings in editor and in build ("allocations are more than 4 frames old") that severely affect the framerate. This is an issue with the Netcode package that should be solved in future versions
- [OnlineFPS Sample] Player input will fail to be processed on some frames, once in a while. This appears to be an issue with the Netcode package, and is related to remote player prediction. It should be solved in future Netcode versions