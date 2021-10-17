
# Changelog

[Previous Versions](./Changelog/changelog-archive.md)

## v0.3
**Upgrade Tips:**
-  As part of a fix for the implementation of `PreventGroundingWhenMovingTowardsNoGrounding` in standard characters, the call to `KinematicCharacterUtilities.DefaultMethods.DetectFutureSlopeChange()` has been **removed** from `characterProcessor.IsGroundedOnHit()`, and **moved** to `characterProcessor.OnUpdate()`. If you already had an existing character before upgrading, you may want to apply this fix to your character as well. Consult [this page](./Changelog/UpgradeTips/changelog-0.3-detectfutureslopeupgrade.md) for details


**Changes:**
- Removed the "Character Controller Wizard" window that generated character scripts from a template. This was done in order to focus on the "Standard Characters" packages as the main way to get started with Rival. Due to the differences between first-person and third-person characters, and all the particularities involved in their authoring/prefab setup, the standard characters are a safer and easier way to get started. This change also allows the learning resources to be more focused on only one approach. (Do note that characters that were previously generated with the "Character Controller Wizard" are still valid even though the window has been removed)
- Improved usability of FirstPerson StandardCharacter with a "FirstPersonCharacterUtilities" class that contains various methods for computing rotations for first person characters. The code of the FirstPerson character systems has been updated to use these methods
- Separated Standard Character packages in two distinct packages: FirstPerson and ThirdPerson
- Added default internal buffer capacities to various character DynamicBuffers 
- Changed the way we handle the framerate-independence of look input in Samples and Standard Characters. The look input is now expected to already be a delta for this frame, so it doesn't get multiplied by deltaTime when applied. This has been done due to the fact that raw mouse input is already a mouse delta for the frame in Unity's input system. Joystick look input, on the other hand, is not multiplied by deltaTime
- Changed the required parameters for PhysicsUtilities.GetHitFaceNormal()
- [Documentation] Made significant changes to the "Getting Started", "Core", "Tutorial", and "Standard Characters" sections of the documentation following the shift from the "Character Controller Wizard" window to the Standard Characters
- [Platformer Sample] Made character orient itself towards rope direction when in RopeSwing state
- [OnlineFPS Sample] Made general cleanup and improvements to the OnlineFPS sample code overall, especially regarding the handling of networked railgun VFX spawning


**Fixes:**
- Fixed an issue where the `PreventGroundingWhenMovingTowardsNoGrounding` option of standard & sample characters caused velocity stoppage in certain scenarios where we were moving near ledges.
- Fixed potential bug when a moving parent would make a character standing on it not collide with obstacles properly
- Fixed a faulty condition check for whether or not the character was overlapping with anything at the beginning of its update
- Fixed lerp calls where the ratio parameter was not clamped
- Made the SamplesBootstrap ignore all sample systems when not in a sample scene
- [Platformer Sample] Fixed issue where a character would always orient itself towards world forward when standing idle on water surface
- [Platformer Sample] Fixed a leak in the SimpleAnimation system
- [Platformer Sample] Fixed issue with skewed orientation of character while climbing
- [Platformer Sample] Fixed jumpy camera transitions when alternating rapidly between crouched and uncrouched states


**Known Issues:**
- Step Handling can cause errors when Burst compilation is disabled. This issue will require a future Unity.Physics package upgrade before it can be solved.
- [OnlineFPS Sample] In Unity 2020.3.16 and over, the console outputs errors during play mode for the OnlineFPS sample. However, this is only in the editor; not in builds. This issue is caused by an incompatilibity between the Unity.Netcode package and Unity 2020.3.16+ versions
- [OnlineFPS Sample] Jitter is visible on remote client characters. This issue will require a future Unity.NetCode package upgrade before it can be solved.
- [OnlineFPS Sample] Errors pop up in the console after compilation: "Couldn't find the TypeDescriptor for the type (...)". However, these errors are inoffensive and can be ignored. This issue will require a future Unity.NetCode package upgrade before it can be solved.
- [OnlineFPS Sample] Lightmaps do not work in builds unless the "OnlineFPS" scene is opened in editor while building. This issue will require a future package upgrade before it can be solved.
- [Platformer Sample] The "standing up from ledge hanging" animation is skipped entirely due to root motion issues. This issue will require a future Unity.Animation package upgrade before it can be solved.