Back to [Samples](../../samples.md)

# OnlineFPS Sample - Game Management

## Connection Menu
The `OnlineFPSMenu` scene contains a simple gameObject-based UI where you can either join an existing game, or host a new game. All logic for this menu is in `OnlineFPSMenuManager`.

Depending on if we are joining or hosting, we use methods from `SamplesBootstrap` in order to create NetCode's client or server worlds (or both). After creating the required ECS worlds, we handle listening or connecting to the right IP, and we load the `OnlineFPS` scene.

## Game Management
`GameManagementSystems.cs` contains several systems that handle various aspect of managing the game flow (initialization, spawning, death, etc...). The following is a description of the main jobs scheduled by each system:

`CommonGameSystem` (handles both client and server logic)
- `PlayerControlledCharacterSetupJob`: Whenever we detect a change in a player's controlled character entity, we do the initial setup for that character (remember its `OwningPlayer`)

`ClientGameSystem` (handles client logic)
- `ClientJoinGameRequestJob`: If the local connection is not marked with a `NetworkStreamInGame` component, send an RPC to the server asking to stream this connection in game
- `ClientInitSpawnedCharactersJob`: Initialized character entities without an `IsInitialized` component. We spawn a nameplate, and if it's the local-owned character, we disable its render meshes and add the `MainEntityCamera` component to the character view (this makes the GameObject camera copy that entity's transform)
- `DisplayRespawnTimerJob`: Handles showing the respawn countdow screen when we receive a certain RPC from server
- `ClientDisconnectJob`: Handles returning to menu scene upon disconnection
- `ClientCharacterDeathSparksJob`: Handles spawning VFX upon receiving a  character death RPC from server

`ServerGameSystem` (handles server logic)
- `ConnectionSetupJob`: Handles remembering the various entities owned by each connection, via the `ConnectionOwnedEntity` buffer
- `ServerHandleClientJoinJob`: Handle client requests to jon the game; spawns a Player and Character prefab for that connection, and stream connection in game
- `ServerOnClientDisconnectJob`: Handles what happens when a client disconnects: clean up all of that client's owned entities
- `ServerSpawnCharactersJob`: Processes all `CharacterSpawnRequest`s. This sets up a character with a weapon properly
- `ServerDetectCharacterDeathJob`: Detects when a character dies, sends an RPC to clients for spawning death VFX, and creates a `CharacterSpawnRequest` with a timer to eventually respawn the character

