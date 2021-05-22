

# Strafing (b-hopping)

Strafing (or "b-hopping") is the consequence of calculating character air movement in certain way. With this way calculating it, having a target velocity that constantly rotates will result in the character gaining more and more speed.

Rival's `CharacterControlUtilities.StandardAirMove` (which is used in the default generated character's `HandleCharacterControl`) has a last parameter named `forceNoMaxSpeedExcess`. If this is set to false (which it is by default), and give your character high-enough acceleration, you will have a character that can de b-hopping

Here is a top-down visualization of the "Basic Sample" character's velocity (in cyan) with and without `forceNoMaxSpeedExcess`. You can notice that when it is false, and the character rotates while moving sideways, its velocity keeps growing

`forceNoMaxSpeedExcess` = true:

![](../Images/strafing-off.png)

`forceNoMaxSpeedExcess` = false:

![](../Images/strafing-on.png)