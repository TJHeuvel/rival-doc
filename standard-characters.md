
# Standard Characters & Cameras

This is a downloadable package containing the following:
* First person character scripts & prefabs
* Third person character scripts & prefabs
* Orbit Camera scripts & prefabs
* A "main camera" system to make it easier to deal with cameras in DOTS, while we wait for DOTS-compatible cameras

[Download](./Downloads/Rival_StandardCharactersAndCameras.unitypackage)


## Main Camera
Since Cameras can't be converted yet in DOTS, we have to do a little workaround to let us implement camera controls in components & systems:
* Have a regular GameObject camera in your scene (not in a subscene)
* Add a `MainGameObjectCamera` to that GameObject. This will tell the `MainCameraSystem` that this is the camera we want to use for rendering
* Add a `MainEntityCamera` to a Subscene GameObject that is meant to represent the controllable camera entity.

Every frame, the `MainCameraSystem` will then copy the position/rotation of the `MainEntityCamera` to the `MainGameObjectCamera`

See [Tutorial - Camera Setup](./Tutorial/tutorial-camera.md) for more detailed instructions


## First Person Character

#### Setup
Here is how to set up a First Person Character in your Subscene:
* Add the `FirstPersonCharacter` prefab to your Subscene
* Add the `FirstPersonPlayer` prefab to your Subscene
* Assign the `FirstPersonCharacter` to the `ControlledCharacter` field of `FirstPersonPlayer`
* The `View` object under the `FirstPersonCharacter` is what should represent the camera. You can add a `MainEntityCameraAuthoring` component to it in the inspector, or add the `MainEntityCamera` component to it via script

#### Overview
* The `FirstPersonCharacter` authoring component holds a reference to a `CharacterViewTransform`. This transform is meant to represent the "camera point" of the first person character, and must be a direct child of the root character object. During conversion, this becomes the `FirstPersonCharacterComponent.CharacterViewEntity`
* `FirstPersonPlayer` has configurable look rotation speed & min/max angles
* `FirstPersonPlayerSystem` handles input. It makes the character move relatively to the character transform orientation, and also calculates a final look direction for the character's `CharacterViewEntity`
* The first person character has a particularity compared to Rival's default generated character; it handles character rotation in a separate system that updates on variable update. This is preferable for first person characters who must rotate 1:1 with their camera, which also rotates on a variable update. Therefore, `FirstPersonCharacterMovementSystem` handles position movement on fixed update, and `FirstPersonCharacterRotationSystem` handles rotation on variable update.
* `FirstPersonCharacterRotationSystem` takes the desired look direction calculated by the `FirstPersonPlayerSystem`, makes the character transform orient its forward towards it (without changing its up direction), and makes the `CharacterViewEntity` point directly towards it.


## Third Person Character

#### Setup
Here is how to set up a Third Person Character in your Subscene:
* Add the `ThirdPersonCharacter` prefab to your Subscene
* Add the `ThirdPersonPlayer` prefab to your Subscene
* Add the `OrbitCamera` prefab to your Subscene
* Assign the `ThirdPersonCharacter` to the `ControlledCharacter` field of `ThirdPersonPlayer`
* Assign the `OrbitCamera` to the `ControlledCamera` field of `ThirdPersonPlayer`
* Assign either the `ThirdPersonCharacter` or a child object of it to the `InitialFollowedObject` field of `OrbitCameraAuthoring`
* Assign the `ThirdPersonCharacter` to the `IgnoredEntities` list of `OrbitCameraAuthoring`
* You can add a `MainEntityCameraAuthoring` component to the `OrbitCamera` in the inspector, or add the `MainEntityCamera` component to it via script

#### Overview
* This character is nearly identical to the basic [Tutorial](./tutorial.md) character at the step where we implement input.
* `OrbitCamera` holds parameters for rotation speed, min/max angles, obstruction detection, etc....