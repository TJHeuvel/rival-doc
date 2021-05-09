

# OnlineFPS Sample - Game Management

## Connection Menu
The `OnlineFPSMenu` scene contains a simple gameObject-based UI where you can either join an existing game, or host a new game. All logic for this menu is in `OnlineFPSMenuManager`.

Depending on if we are joining or hosting, we use methods from `SamplesBootstrap` in order to create NetCode's client or server worlds (or both). After creating the required ECS worlds, we handle listening or connecting to the right IP, and we load the `OnlineFPS` scene.

## Client connection request
A `MapIsLoaded` component is present in the `OnlineFPS` scene. The `ClientGameSystem` waits for this component to exist in order to determine when the scene is actually fully loaded. Once this is done, it sends an `RPCJoinGameRequest` RPC to the server asking to officially join the game as a player.

## Server connection request reception and player setup
When the server receives a `RPCJoinGameRequest` from a client in `ServerGameSystem`, it spawns a Player ghost entity for that client, and creates a `CharacterSpawnRequest` for that player. We separate the concepts of "Player" and "Character", because a player persists even when a character dies and respanws. You can think of the "Character" as the current thing that the "Player" controls.

## Character spawning & respawning
Character spawning is handled bythe `CharacterSpawningSystem`. This waits for `CharacterSpawnRequest` entities to be created, and spawns a character assigned to the desired player for each request. `CharacterDeathSystem` handles destroying the character entity and initiating the respawn sequence by creating a new `CharacterSpawnRequest` with a timer. 

## Client player & character setup
The `ClientGameSystem` iterates on character & player entities that don't have any `IsInitialized` components. For uninitialized player entities, it adds a `OnlineFPSPlayerInputs` component to them, and sets that entity as the commands target entity. For uninitialized character entities, it spawns a gameObject-based nameplate over the character.

## Disconnection
In `ClientGameSystem`, when we detect that our `NetworkIdComponent` representing the connection to the server has disconnected (when there is a `NetworkStreamDisconnected` on that connection entity), we simply reload the menu scenes

In `ServerGameSystem`, when we detect that a client connection has disconnected similarly, we go thorough all entities that have been registered as belonging to that connection in the `ConnectionOwnedEntity` DynamicBuffer on that connection entity, and we destroy them. That way, the Character and Player entities of disconnected clients get cleaned up.