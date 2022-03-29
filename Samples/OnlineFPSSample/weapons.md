Back to [Samples](../../samples.md)

# OnlineFPS Sample - Weapons

## Assigning a weapon to a character
`ServerGameSystem` handles spawning a weapon for each character that it spawns, and assigning it as the character's `ActiveWeapon`. This is done in the job named "ServerSpawnCharactersJob"

Then, `WeaponAssignmentSystem` iterates on all `ActiveWeapon` (on all characters), and handles doing the weapon setup whenever a change in active weapon is detected. Here we handle parenting the weapon to the FPS character view transform, and we set the weapon's `ShootOriginOverride` as our view entity. This is a trick most FPS games use to make sure the weapon shot raycasts start from the center of the camera and land exactly in the middle of the screen.


## Weapon shooting
First, the `OnlineFPSPlayerControlSystem` (part of the `GhostPredictionSystemGroup`) reads player commands and sets `weapon.ShootRequested = playerCommands.ShootRequested;` on the player's weapon.

Then, `RailgunSystem` (also part of the `GhostPredictionSystemGroup`) iterates on every `Railgun` + `Weapon`, and if `weapon.ShootRequested` is true, it handles shooting


## Weapon shot VFX
The railgun shot VFX requires some special handling due to the fact that shots are part of the prediction group. If we spawned the lazer VFX every time the weapon shot, we would often end up spawning the vfx multiple times, because of the rollback and re-simulation associated with the prediction system. 

To solve this issue, the `RailgunSystem` will only spawn shot VFX on ticks that are higher than the last tick we remember shooting at (this last tick is store in the `Railgun` component, as `_lastTickShot`)


## Weapon animation
`WeaponAnimationSystem` Handles weapon bobbing, recoil, and aiming FOV zooming.