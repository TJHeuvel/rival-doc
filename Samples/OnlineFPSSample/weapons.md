Back to [Samples](../../samples.md)

# OnlineFPS Sample - Weapons

## Assigning a weapon to a character
`CharacterSpawningSystem` handles spawning and assigning a weapon to a character when spawned.

`WeaponAssignmentSystem` Handles assigning a weapon ghost instance to a character, as the "active" held weapon. It looks for active weapon ghost Id changes in the `ActiveWeapon` component, and parents the new active weapon's ghost entity to the character's active weapon socket transform if a change was detected.

## Weapon shooting
Several weapons systems are part of the `GhostPredictionSystemGroup`, because lag-compensation is crucial for their accuracy in fast-paced games.

`ShootingSystem` Handles listening for shoot input based on the `OnlineFPSPlayerCommands` of a given player, and then informs the `Weapon` component of the active weapon that a shot is requested.

`RailgunSystem` Handles the actual shooting for the railgun weapon. It looks at the `Weapon` component to determine if a shot is requested, and if so, handles all the firing logic (raycast for shot hits, apply damage, handle firing rate, create VFX events, etc....).

## Weapon shot VFX
The railgun shot VFX requires some special handling due to the fact that shots are part of the prediction group. If we spawned the lazer VFX every time the weapon shot, we would often end up spawning the vfx multiple times, because of the rollback and re-simulation associated with the prediction system. To solve this issue, we add shot events to a `DynamicBuffer<RailgunVisualShootEvent>` on the railgun entity during the update of the `RailgunSystem`, but we only process those events in the `RailgunVisualShootEventSystem` which runs in the regular non-predicted `SimulationSystemGroup`. This means we'll only spawn the railgun shot VFX if it shot on the last frame that was simulated.

On the server, `RailgunVisualShootEventSystem` will send a `RailgunVisualShootEventRPC` to all clients except the owner of the railgun, asking them to spawn that VFX as well. Those RPCs are processed in `RailgunVisualShootEventRPCReceiveSystem`


## Weapon animation
`WeaponAnimationSystem` Handles weapon bobbing, recoil, and aiming FOV zooming.