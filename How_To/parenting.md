

# Parenting

You are not allowed to make the Character object a child of another transform in the usual way (`Parent` component). However, you can do so by setting `KinematicCharacterBody.ParentEntity` to the desired parent entity.

Note 1: if you want to change the `ParentEntity`, you will have to remove the `KinematicCharacterUtilities.DefaultMethods.MovingPlatformDetection` step of your character processor's `OnUpdate`. Moving platform detection automatically assigns any valid moving ground entity as the current `KinematicCharacterBody.ParentEntity`, or assigns `Entity.Null` if no valid ground is detected. So keeping it in your `OnUpdate` would overwrite any parent you try to manually assign.

Note 2: in the `KinematicCharacterUtilities.DefaultMethods.ParentMovementUpdate` step of your character processor's `OnUpdate`, the last parameter of the function is `constrainRotationToGroundingUp`. When this parameter is true, the character will always stay upright regardless of how the parent rotates. This is useful for typical moving platform behaviour, but you might want to set it to false if you don't want this feature.
