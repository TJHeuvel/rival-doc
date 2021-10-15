
# Standard Characters - Third Person Character

## Setup
Here is how to set up a Third Person Character:
* Export the `StandardCharacter_ThirdPerson` package, found under the "Rival/StandardCharacters" folder
* Add the `ThirdPersonCharacter`, `ThirdPersonPlayer`, and `OrbitCamera` prefabs to your Subscene [[Screenshot]](../Images/stdcharacters-tp1.png)
* Assign the `ThirdPersonCharacter` to the `ControlledCharacter` field of `ThirdPersonPlayer`, and assign the `OrbitCamera` to the `ControlledCamera` field of `ThirdPersonPlayer` [[Screenshot]](../Images/stdcharacters-tp2.png)
* Assign the `ThirdPersonCharacter` to the `FollowedCharacter` field of `OrbitCameraAuthoring` [[Screenshot]](../Images/stdcharacters-tp3.png)
* Optional: on the `ThirdPersonCharacter` object, you can add a `CameraTarget` component to specify a child transform of the character that will act as the real target of the camera. [[Screenshot]](../Images/stdcharacters-tp4.png)
* You can add a `MainEntityCameraAuthoring` component to the `OrbitCamera` in the inspector (or add the `MainEntityCamera` component to it via script) [[Screenshot]](../Images/stdcharacters-tp5.png)

Feel free to rename the exported class/struct/file names at this point using a refactoring tool. Since these files are exported as copies from unitypackages into your project, future Rival updates will not overwrite your changes.


## Overview

### General

### Components
* `OrbitCamera` holds parameters for rotation speed, min/max angles, obstruction detection, etc....

### Systems

### Processor