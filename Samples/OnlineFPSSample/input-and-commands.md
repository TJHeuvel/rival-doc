Back to [Samples](../../samples.md)

# OnlineFPS Sample - Input and Player Commands

`OnlineFPSPlayerCommandsSystem` gathers input from Unity's input system, and writes those inputs directly into a buffer of `OnlineFPSPlayerCommands`

However, we check in the `OnlineFPSPlayerCommands` buffer if there already were commands written for the current tick. If so, we will "merge" these commands together rather than simply overwriting them. We do this in order to avoid having some input "fall between ticks". It's a concept that's similar to how we must handle input in an offline game where input-dependent logic must be processed on fixed update.

Later, in the prediction update, a `OnlineFPSPlayerControlSystem` will read those commands and control characters, aiming & weapons with them