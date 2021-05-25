Back to [Samples](../../samples.md)

# Basic Sample - Character

The Basic character uses a structure that is very similar to the default code-generated character. The main additions are found in the `BasicCharacterProcessor`, where logic is implemented to allow the following features:
* Ignore grounding on physics bodies that have a certain physics tag
* Ignore collisions on physics bodies that have a certain physics tag
* Ignore step handling on physics bodies that have a certain physics tag
* Ignore slope changes handling on physics bodies that have a certain physics tag
* Modify mass ratios against certain dynamic physics bodies based on a physics tag
* Modify velocity projection to allow bouncing against certain physics bodies based on a physics tag


## AI Characters
AI characters use the exact same system as the main player-controlled character, but their `BasicCharacterInputs` component is being controlled by the `BasicCharacterAISystem` instead of the `BasicPlayerInputsToCharacterInputsSystem` like the player-controlled character does.