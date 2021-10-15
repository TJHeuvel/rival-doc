
# Standard Characters - First Person Character

## Setup
Here is how to set up a First Person Character:
* Export the `StandardCharacter_FirstPerson` package, found under the "Rival/StandardCharacters" folder
* Add the `FirstPersonCharacter` and `FirstPersonPlayer` prefabs to your Subscene [[Screenshot]](../Images/stdcharacters-fp1.png)
* Assign the `FirstPersonCharacter` to the `ControlledCharacter` field of `FirstPersonPlayer` [[Screenshot]](../Images/stdcharacters-fp2.png)
* Add a `MainEntityCameraAuthoring` component toThe `View` object under the `FirstPersonCharacter`. The `View` object is what should represent the "camera point" of the first person character. [[Screenshot]](../Images/stdcharacters-fp3.png)

Feel free to rename the exported class/struct/file names at this point using a refactoring tool. Since these files are exported as copies from unitypackages into your project, future Rival updates will not overwrite your changes.


## Overview

### General
* The `FirstPersonCharacter` authoring component holds a reference to a `CharacterViewTransform`. This transform is meant to represent the "camera point" of the first person character, and must be a direct child of the root character object. During conversion, this becomes the `FirstPersonCharacterComponent.CharacterViewEntity`
* `FirstPersonPlayerSystem` handles input. It makes the character move relatively to the character transform orientation, and also calculates a final look direction for the character's `CharacterViewEntity`

### Components
* `OrbitCamera` holds parameters for rotation speed, min/max angles, obstruction detection, etc....

### Systems
* `FirstPersonCharacterMovementSystem` handles position movement on fixed update
* `FirstPersonCharacterRotationSystem` handles rotation on variable update.

### Processor
