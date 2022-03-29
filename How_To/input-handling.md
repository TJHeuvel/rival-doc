Back to [How To](../how-to.md)

# Input Handling

## CharacterInputs Component
The standard characters have the concept of a "CharacterInputs" component on the character entity. This component is not an absolute requirement, but it is a suggestion of how to structure things for good versatility.

The purpose of this component is to convert player input (gamepad/keyboard/mouse button presses) into a representation that the character understands easily. Essentially; we want the character to not need to understand how to interpret raw input presses. Instead, we want to tell it more directly how to move.

What's very useful about this approach is that your character now becomes able to be controlled by anything; whether it's a human player or an AI. If your characterInputs component contains fields for `MoveVector` and `LookDirection`:
- A human player can read gamepad inputs and transform those values to `MoveVector` and `LookDirection` in order to make the character move
- An AI controller can analyze the world with sensors, make decisions, and feed values to `MoveVector` and `LookDirection` in order to make the character move

The result is a single character implementation, with a common interface for being controlled by anything


## Fixed Step Input
There is one important particularity to keep in mind when dealing with input for a character controller that updates at a fixed time step: punctual input presses (like detecting when a key was just pressed down) may get lost between two fixedstep updates, since they are usually processed at a variable time step.

The solution, for properly detecting those punctual input presses, is to remember if they have happened at least once since the last time we did a fixedstep update. The standard characters show how to deal with this in their player systems (ex: `FirstPersonPlayerSystem`). We first get the tick # of our fixed update by using `FixedUpdateTickSystem`, and then, if we determine that this tick is still the same as the last tick when we processed input, we will "combine" our new inputs to the old inputs, instead of replacing them completely. The "Jump" button handling in the standard characters is an example of this