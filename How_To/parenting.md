Back to [How To](../how-to.md)

# Parenting the Character to Another Object

You are not allowed to make the Character object a child of another transform in the usual way (`Parent` component). However, you can do so by setting `KinematicCharacterBody.ParentEntity` to the desired parent entity.

You can use `KinematicCharacterUtilities.SetOrUpdateParentBody`in order to set a parent entity for a character. However, you must remember to call this on every frame where you want the parenting behaviour to be active. This is because the common use case for character parenting is moving platforms, and in those cases, the "anchor point" of the parenting will change from frame to frame based on where the character collider makes contact with the ground.

Note 1: if you want to change the `ParentEntity`, you will have to remove the `KinematicCharacterUtilities.DefaultMethods.MovingPlatformDetection` step of your character processor's `OnUpdate`. Moving platform detection automatically assigns any valid moving ground entity as the current `KinematicCharacterBody.ParentEntity`, or assigns `Entity.Null` if no valid ground is detected. So keeping it in your `OnUpdate` would overwrite any parent you try to manually assign.