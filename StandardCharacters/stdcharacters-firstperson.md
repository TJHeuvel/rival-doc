
# Standard Characters - First Person Character

## Setup
Here is how to set up a First Person Character:
* Export the `StandardCharacter_FirstPerson` package, found under the "Rival/StandardCharacters" folder
* Add the `FirstPersonCharacter` and `FirstPersonPlayer` prefabs to your Subscene [[Screenshot]](../Images/stdcharacters-fp1.png)
* Assign the `FirstPersonCharacter` to the `ControlledCharacter` field of `FirstPersonPlayer` [[Screenshot]](../Images/stdcharacters-fp2.png)
* The `View` object under the `FirstPersonCharacter` is what should represent the camera. You can add a `MainEntityCameraAuthoring` component to it in the inspector (or add the `MainEntityCamera` component to it via script) [[Screenshot]](../Images/stdcharacters-fp3.png)

Feel free to rename the exported class/struct/file names at this point using a refactoring tool. Since these files are exported as copies from unitypackages into your project, future Rival updates will not overwrite your changes.

## Code Overview
* The `FirstPersonCharacter` authoring component holds a reference to a `CharacterViewTransform`. This transform is meant to represent the "camera point" of the first person character, and must be a direct child of the root character object. During conversion, this becomes the `FirstPersonCharacterComponent.CharacterViewEntity`
* `FirstPersonPlayer` has configurable look rotation speed & min/max angles
* `FirstPersonPlayerSystem` handles input. It makes the character move relatively to the character transform orientation, and also calculates a final look direction for the character's `CharacterViewEntity`
* The first person character has a particularity compared to Rival's default generated character; it handles character rotation in a separate system that updates on variable update. This is preferable for first person characters who must rotate 1:1 with their camera, which also rotates on a variable update. Therefore, `FirstPersonCharacterMovementSystem` handles position movement on fixed update, and `FirstPersonCharacterRotationSystem` handles rotation on variable update.
* `FirstPersonCharacterRotationSystem` takes the desired look direction calculated by the `FirstPersonPlayerSystem`, makes the character transform orient its forward towards it (without changing its up direction), and makes the `CharacterViewEntity` point directly towards it.


