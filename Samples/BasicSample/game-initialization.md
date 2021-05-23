Back to [Samples](../../samples.md)

# Basic Sample - Game Initialization

`BasicSceneInitializationSystem` is responsible game initialization. It begins by spawning the camera and creating a `CharacterSpawnRequest` for the initial character. It then listens for `CharacterSpawnRequest` components to exist in order to spawn a character from a character prefab, and assign that character as the camera followed entity. Respawning is then simply a question of destroying the current character entity, and creating a new `CharacterSpawnRequest`.