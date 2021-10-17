
# Standard Characters - First Person Character

## Setup
Here is how to set up a First Person Character:
* Export the `StandardCharacter_FirstPerson` package, found under the "Rival/StandardCharacters" folder
* Add the `FirstPersonCharacter` and `FirstPersonPlayer` prefabs to your Subscene [[Screenshot]](../Images/stdcharacters-fp1.png)
* Assign the `FirstPersonCharacter` to the `ControlledCharacter` field of `FirstPersonPlayer` [[Screenshot]](../Images/stdcharacters-fp2.png)
* Add a `MainEntityCameraAuthoring` component to the `View` object under the `FirstPersonCharacter`. The `View` object is what should represent the "camera point" of the first person character. [[Screenshot]](../Images/stdcharacters-fp3.png)

Feel free to move the files to other folders and rename the exported class/struct/file names using a refactoring tool. Since these files are exported as copies from unitypackages into your project, future Rival updates will not overwrite your changes.


## Overview

### General
The FirstPerson character has two main prefabs:
* **FirstPersonPlayer**: This represents the abstract "human player" object that *controls* the character object (gathers input and sends it to its `ControlledCharacter`)
* **FirstPersonCharacter**: This is the actual character controller object

There are usually two reasons why we find it useful to make the "player" and the "character" be two distinct objects:
* This setup makes it easy to create both human controllers and AI controllers that can "take control" of the same kind of character prefab, without having to write different character scripts for human & AI characters
* In typical games, when a character dies and its object is destroyed, we often want the concept of the "player" to persist nonetheless. The player can keep holding data (score, etc....) even though its controlled character has been destroyed, can switch to controlling a spectator camera while awaiting respawn, and can take back control of a newly-spawned character object once their character has respawned. This setup also facilitates switching a player's controlled character/vehicle/etc... during play

First person characters have a `View` object as a child of the main character prefab. This object represents the point on the character that determines where the character is looking/aiming, and this is where the camera should be. The `View` will rotate along with the character, but it will also have its pitch angle rotated independently from the character in order to look up and down.

First person characters have a particularity when it comes to their rotation handling: while their movement translation is handled in a fixed timestep system as usual, their rotation is handled in a different *variable timestep* system. This is done in order to make the character's camera rotation controls feel as fluid & responsive as possible. Handling rotation at a fixed timestep would result in controls that feel muddy and unresponsive, even if we used interpolation. This is why the first person character uses two distinct systems: `FirstPersonCharacterMovementSystem` and a `FirstPersonCharacterRotationSystem`


### Components
* `FirstPersonPlayer`: represents a human player and remembers the player's `ControlledCharacter`
* `FirstPersonCharacterComponent`: holds all character movement parameters
* `FirstPersonCharacterInputs`: holds the data representing the *intentions* of the controller controlling this character (either human or AI). These are things like "where to move" (`MoveVector`), "how to rotate" (`LookYawPitchDegrees`), and "when to jump" (`JumpRequested`)


### Systems
* `FirstPersonPlayerSystem` handles input and sets that input data into the character's `FirstPersonCharacterInputs` component
* `FirstPersonCharacterMovementSystem` handles character translation movement & collision detection on fixed update
* `FirstPersonCharacterRotationSystem` handles the rotation of both the parent character object & the `View` object on variable update.
