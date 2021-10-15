
# Changelog

[Previous Versions](./Changelog/changelog-archive.md)

## v0.3
**Changes:**
- Removed the "Character Controller Wizard" window that generated starting character scripts from a template. This was done in favor of focusing on the "Standard Characters" packages as the main way to get started in Rival. Due to the differences between first-person and third-person characters, and all the particularities involved in their authoring/prefab setup, the standard characters are a safer and easier way of getting started. The tutorial section of the documentation has been updated accordingly
- Improved usability of FirstPerson StandardCharacter with a "FirstPersonCharacterUtilities" class that contains various methods for computing rotations for first person characters. The code of the FirstPerson character systems has been updated to use these methods
- Added default internal buffer capacities to various character DynamicBuffers 
- Changed the required parameters for PhysicsUtilities.GetHitFaceNormal()
- [Platformer Sample] Made character orient itself towards rope direction when in RopeSwing state
- [OnlineFPS Sample] Made general cleanup and improvements to the sample code overall, especially regarding the handling of networked railgun VFX


**Fixes:**
- Fixed an issue where the "PreventGroundingWhenMovingTowardsNoGrounding" option of standard & sample characters cause velocity stoppage in certain scenarios. As a consequence, the call to "KinematicCharacterUtilities.DefaultMethods.DetectFutureSlopeChange()" has been moved from "IsGroundedOnHit()" to "HandleCharacterControl()"
- Fixed potential bug when a moving parent would make a character not collide with obstacles properly
- Fixed a faulty condition check for whether or not the character was overlapping with anything at the beginning of its update
- Fixed framerate-independence of look input in Samples and Standard Characters
- Fixed lerp calls where the ratio parameter was not clamped
- [Platformer Sample] Fixed issue where character would always orient itself towards world forward when standing idle on water surface
- [Platformer Sample] Fixed a leak in the SimpleAnimation system
- [Platformer Sample] Fixed issue with skewed orientation of character while climbing
- [Platformer Sample] Fixed jumpy camera transitions when alternating rapidly between crouched and uncrouched states


**Known Issues:**
- Step Handling can cause errors when Burst compilation is disabled. This issue will require a future Unity.Physics package upgrade before it can be solved.
- [OnlineFPS Sample] In Unity 2020.3.16 and over, the console outputs errors during play mode. However, this is only in the editor; not in builds. This issue is caused by an incompatilibity between the Unity.Netcode package and later Unity 2020.3 versions
- [OnlineFPS Sample] Jitter is visible on remote client characters. This issue will require a future Unity.NetCode package upgrade before it can be solved.
- [OnlineFPS Sample] Errors pop up in the console after compilation: "Couldn't find the TypeDescriptor for the type (...)". However, these errors are inoffensive and can be ignored. This issue will require a future Unity.NetCode package upgrade before it can be solved.
- [OnlineFPS Sample] Lightmaps do not work in builds unless the "OnlineFPS" scene is opened in editor while building. This issue will require a future package upgrade before it can be solved.
- [Platformer Sample] The "standing up from ledge hanging" animation is skipped entirely due to root motion issues. This issue will require a future Unity.Animation package upgrade before it can be solved.