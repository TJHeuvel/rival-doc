

# Slopes, Ledges and Denivelations

There are times when you might want your character to not be grounded when standing on a ledge, or not be grounded when going over a downward change in slope angle (so your character can "launch" off the slope instead of sticking to it).

You can have the control you need in these situations by using the `IKinematicCharacterProcessor.IsGroundedOnHit` callback of your character processor, combined with the following utilities for calculating `DenivelationDetails`:
* `KinematicCharacterUtilities.ShouldPreventGroundingBasedOnDenivelations`: this is a quick pre-made function that tells you if grounding should be prevented based on being on the falling side of a ledge, or when going over a certain denivelation angle. In the Basic sample, you can see an example usage of this in `BasicCharacterProcessor.IsGroundedOnHit`
* `KinematicCharacterUtilities.CalculateDenivelationDetailsOfHit`: this is a function used internally by the previous function. You can use this instead if you need access to the calculated DenivelationDetails, but for different scenarios than the ones provided by the previous function.

The `DenivelationDetails` struct contains all sorts of information about the ground you're currently standing on: ledges, angles, directions of ridges, etc.... You can use this information in any way you want.

Be aware, however, that calculating `DenivelationDetails` has the performance cost of a few raycasts. This is why it's up to users to decide if they want to use it or not.