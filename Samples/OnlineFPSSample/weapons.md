Back to [Samples](../../samples.md)

# OnlineFPS Sample - Weapons

## Assigning a weapon to a character
`CharacterSpawningSystem` handles spawning and assigning a weapon to a character when spawned.

`WeaponAssignmentSystem` Handles assigning a weapon ghost instance to a character, as the "active" held weapon. It looks for active weapon ghost Id changes in the `ActiveWeapon` component, and parents the new active weapon's ghost entity to the character's active weapon socket transform if a change was detected.

## Weapon shooting
Several weapons systems are part of the `GhostPredictionSystemGroup`, because lag-compensation is crucial for their accuracy in fast-paced games.

`ShootingSystem` Handles listening for shoot input based on the `OnlineFPSPlayerCommands` of a given player, and then informs the `Weapon` component of the active weapon that a shot is requested.

`RailgunSystem` Handles the actual shooting for the railgun weapon. It looks at the `Weapon` component to determine if a shot is requested, and if so, handles all the firing logic (raycast for shot hits, apply damage, handle firing rate, spawn VFX, etc....).

The railgun shot VFX requires some special handling due to the fact that shots are part of the prediction group. If we spawned the lazer VFX every time the weapon shot, we would often end up spawning the vfx multiple times. This is because of the rollback and re-simulation associated with the prediction system. The shooting of the weapon will be re-simulated several times on the frames folloing the actual shot during prediction. To solve this issue, we only spawn the shot VFX if the shot is happening on the tick that represents the "present" frame (as opposed to something that happened in the past and is being re-simulated). `PredictionInitializationSystem.IsFirstFullPresentTick` handles calculating that condition for us.

## Weapon animation
`WeaponAnimationSystem` Handles weapon bobbing, recoil, and aiming FOV zooming.