

# Platformer Sample - Character

## Character

The character in this sample is fairly complex. Due to its level of complexity, we've decided to separate its movement logic into various "states".

`PlatformerCharacterProcessor.OnUpdate` starts by handling logic that is common to all states, then calls the current state's update via the `PlatformerCharacterStateMachine` component, and finally does more logic common to all states.

To allow more flexibility in state logic, the usual core character update methods are called by the individual state updates, rather than by the `PlatformerCharacterProcessor.OnUpdate` directly. `PlatformerCharacterProcessor` also contains various utility functions that could be used by several states. 


## State Machine

`PlatformerCharacterStateMachine` holds structures corresponding to all character states, remembers which state is the current one, and contains functions to call `OnStateEnter`, `OnStateExit`, and `OnStateUpdate`. These functions work by doing a switch case over the `CharacterState` enum, and calling the right function in the state struct corresponding to that `CharacterState`.

The `PlatformerCharacterProcessor` passes itself by reference to all state function calls, so that these state functions can have access to all the data that the processor holds.

State transitions are detected by the state themselves in their update, and the transition is done by calling `PlatformerCharacterProcessor.TransitionToState`.


## States

Here are the various states of the character:

- [Ground Move](CharacterStates/ground-move)
- [Air Move](CharacterStates/air-move)
- [Crouched](CharacterStates/crouched)
- [Wall Run](CharacterStates/wall-run)
- [Flying](CharacterStates/flying)
- [Dashing](CharacterStates/dashing)
- [Rope Swing](CharacterStates/rope-swing)
- [Rolling](CharacterStates/rolling)
- [Swimming](CharacterStates/swimming)
- [Climbing](CharacterStates/climbing)
- [Ledge Grab](CharacterStates/ledge-grab)
- [Disabled](CharacterStates/disabled)