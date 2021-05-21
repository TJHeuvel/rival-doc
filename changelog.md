
# Changelog

## v0.2 (upcoming)
**Features:**
- Added an extra backwards raycast at the end of `DetectFutureSlopeChange`, to improve slope angle change detection

**Fixes**
- Set the default `deltaTimeIntoFuture` value to "Time.DeltaTime", when calling `DetectFutureSlopeChange` in the `IsGroundedOnHit` callback of the default generated character processor

## v0.1 (latest)
**Features:**
- Initial package

**Known Issues:**
- [OnlineFPS Sample] Jitter is visible on remote client characters. This issue will require a future Unity.NetCode package upgrade before it can be solved.
- [OnlineFPS Sample] Errors pop up in the console after compilation: "Couldn't find the TypeDescriptor for the type (...)". However, these errors are inoffensive and can be ignored. This issue will require a future Unity.NetCode package upgrade before it can be solved.
- [OnlineFPS Sample] Lightmaps do not work in builds unless the "OnlineFPS" scene is opened in editor while building. This issue will require a future package upgrade before it can be solved.
- [Platformer Sample] The "standing up from ledge hanging" animation is skipped entirely due to root motion issues. This issue will require a future Unity.Animation package upgrade before it can be solved.