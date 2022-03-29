Back to [Samples](../../samples.md)

# Platformer Sample - Animation

Since the DOTS animation package is temporarily unavailable, animation in this sample is handled with a hybrid GameObject/ECS approach for the time being. Here are the main files responsible for animation handling:
- `PlatformerCharacterAnimationAuthoring`: Defines the clip indexes of the various animation clips, as defined in the mecanim animation controller
- `PlatformerCharacterAnimation`: Holds the clip indexes of the various animation clips, as well as the hash for the "clipIndex" parameter in the mecanim animation controller
- `PlatformerCharacterHybridLink`: An ECS hybrid component whose purpose is to remember references to the character skinned mesh GameObject, as well as the mecanim `Animator` component. Notice that this component is of `class` type
- `PlatformerCharacterHybridData`: An ECS hybrid component whose purpose is to remember the prefab of the character skinned mesh GameObject. Notice that this component is of `class` type
- `PlatformerCharacterHybridSystem`: A system that handles instantiating/destroying the character skinned mesh GameObject along with its associated Entity. It also handles updating animation every frame by calling `PlatformerCharacterAnimationHandler.UpdateAnimation()`
- `PlatformerCharacterAnimationHandler`: A static class that handles deciding which animation state the character should have right now. It does this by observing the character state, calculating animation speeds when needed, and setting the "clipIndex" parameter in the mecanim animator accordingly