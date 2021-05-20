

# Slopes, Ledges and Denivelations

There are times when you might want your character to not be grounded when standing on a ledge, or not be grounded when going over a downward change in slope angle (so your character can "launch" off the slope instead of sticking to it). 

The default generated character comes with parameters for this:
* `PreventGroundingWhenMovingTowardsFallingSideOfLedge`: prevents sticking to the ground when moving off of a ledge
* `PreventGroundingWithDenivelationAngle`: prevent sticking to the ground when moving over a downward slope change
* `MaxDenivelationAngleForGrounding`: max angle in degrees where we can stick to the ground when moving over a downward slope change

The reason why this is part of the generated character and not part of `KinematicCharacterBody` is because you may want to choose to customize or replace that functionality.

These are used in your character processor in `IsGroundedOnHit`. Internally, this uses `KinematicCharacterUtilities.ShouldPreventGroundingBasedOnDenivelations`, which uses `KinematicCharacterUtilities.CalculateDenivelationDetailsOfHit`. This calculates information about the ledge & denivelation that you could use for any purpose you see fit. Be aware, however, that calculating `DenivelationDetails` has the performance cost of a few raycasts. This is why it's up to users to decide if they want to use it or not.