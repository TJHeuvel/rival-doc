Back to [Samples](../../samples.md)

# StressTest Sample - Overview

The character in this sample is very close to the standard characters, with the exception that it has options to turn on and off certain features.

`StressTestManager` handles all the rest of the logic in this sample. Here are the various options:
- **Spawn button**: spawns characters 
- **Spawn Count**: amount of characters to be spawned
- **Environment Prefab**: select which environment will be spawned
- **Multithreaded**: toggle scheduling parallel jobs instead of a single job
- **Physics Step**: toggle the simulation of the physics step
- **Rendering**: toggle rendering
- **SlopeAngle**: toggle the detection of slope angle degrounding
- **StepHandling**: toggles step handling
- **AddCloseHitsForVelocityProjection**: toggles `KinematicCharacterBody.AddCloseHitsForVelocityProjection`
- **StatefulHits**: toggles calling `KinematicCharacterUtilities.ProcessStatefulCharacterHits` in the character's update
- **SimulatedDynamic**: toggles `KinematicCharacterBody.SimulatedDynamic` and toggles character collision response between "Trigger" (when true) and "Collide" (when false)