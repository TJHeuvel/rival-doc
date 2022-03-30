
# Standard Characters - Third Person Character

## Setup
Here is how to set up a Third Person Character:
* Export the `StandardCharacter_ThirdPerson` package, found under the "Rival/StandardCharacters" folder
* Add the `ThirdPersonCharacter`, `ThirdPersonPlayer`, and `OrbitCamera` prefabs to your Subscene [[Screenshot]](../Images/stdcharacters-tp1.png)
* Assign the `ThirdPersonCharacter` to the `ControlledCharacter` field of `ThirdPersonPlayer`, and assign the `OrbitCamera` to the `ControlledCamera` field of `ThirdPersonPlayer` [[Screenshot]](../Images/stdcharacters-tp2.png)
* Assign the `ThirdPersonCharacter` to the `FollowedCharacter` field of `OrbitCameraAuthoring` [[Screenshot]](../Images/stdcharacters-tp3.png)
* Optional: on the `ThirdPersonCharacter` object, you can add a `CameraTarget` component to specify a child transform of the character that will act as the real target of the camera. [[Screenshot]](../Images/stdcharacters-tp4.png)
* You can add a `MainEntityCameraAuthoring` component to the `OrbitCamera` in the inspector (or add the `MainEntityCamera` component to it via script) [[Screenshot]](../Images/stdcharacters-tp5.png)

Feel free to move the files to other folders and rename the exported class/struct/file names using a refactoring tool. Since these files are exported as copies from unitypackages into your project, future Rival updates will not overwrite your changes.


## Overview

### General
The ThirdPerson character has three main prefabs:
* **ThirdPersonPlayer**: This represents the abstract "human player" object that *controls* the character object (gathers input and sends it to its `ControlledCharacter`)
* **ThirdPersonCharacter**: This is the actual character controller object
* **OrbitCamera**: This is the controllable camera that orbits around the character object

There are usually two reasons why we find it useful to make the "player" and the "character" be two distinct objects:
* This setup makes it easy to create both human controllers and AI controllers that can "take control" of the same kind of character prefab, without having to write different character scripts for human & AI characters
* In typical games, when a character dies and its object is destroyed, we often want the concept of the "player" to persist nonetheless. The player can keep holding data (score, etc....) even though its controlled character has been destroyed, can switch to controlling a spectator camera while awaiting respawn, and can take back control of a newly-spawned character object once their character has respawned. This setup also facilitates switching a player's controlled character/vehicle/etc... during play

The `ThirdPersonPlayer` controls not only the character, but also the camera (which is a separate object). It acts as a central point for coordinating the two. Since AI characters should not have any concept of a "camera", it is important that the `ThirdPersonCharacterMovementSystem` doesn't have camera knowledge. This is why camera is handled by the "human player" instead.


### Components
* `ThirdPersonPlayer`: represents a human player and remembers the player's `ControlledCharacter`
* `ThirdPersonCharacterComponent`: holds all character movement parameters
* `ThirdPersonCharacterInputs`: holds the data representing the *intentions* of the controller controlling this character (either human or AI). These are things like "where to move" (`MoveVector`), "how to rotate" (`LookYawPitchDegrees`), and "when to jump" (`JumpRequested`)
* `OrbitCamera`: holds all camera movement parameters
* `CameraTarget`: holds an Entity representing the transform of the anchor point of the camera on this character (this anchor point could be a child object)


### Systems
* `ThirdPersonPlayerSystem` handles input and sets that input data into the character's `ThirdPersonCharacterInputs` component
* `ThirdPersonCharacterMovementSystem` handles character translation movement & collision detection on fixed update
* `ThirdPersonCharacterRotationSystem` handles character rotation on variable update
* `OrbitCameraSystem` handles camera movement
