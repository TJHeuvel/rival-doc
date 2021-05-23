Back to [Samples](../../samples.md)

# Basic Sample - Input

`BasicPlayerInputsSystem` gathers input from Unity's input system, and writes those inputs to all `BasicPlayerInputs` components present in the world.

`BasicPlayerInputsToCharacterInputsSystem` takes the inputs from `BasicPlayerInputs` components, and converts them to inputs that the character system can understand in the `BasicCharacterInputs` component.

This separation of input handling in two separate phases ("player" inputs and "character" inputs) is done so that AI characters can easily be supported.