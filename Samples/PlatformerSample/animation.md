

# Platformer Sample - Animation

Animation is handled in `PlatformerCharacterAnimationSystem`. This system iterates on all character animation components, and uses a `SimpleAnimationSystem` to call transitions to animation states depending on the state of the character.

The `SimpleAnimationSystem` is available in the "Common" folder. More information on this system can be found [here](https://forum.unity.com/threads/sources-included-a-simple-animation-system-to-get-started-quickly.1046975/).