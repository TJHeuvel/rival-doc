
Back to [Tutorial](../tutorial.md)

# Tutorial - Camera Setup

Now we will set up a simple camera. You can start by downloading extracting the "StandardCharacters" package that's included with Rival. We won't use the character scripts contained here for the tutorial, but we will use the camera scripts:

We won't go over the camera code in detail, but here is a summary of how it works:
- Since Unity's `Camera` component cannot be converted to DOTS yet, we need to come up with a way to keep the camera GameObject in the scene, but make it copy the transform of an Entity that represents the camera.
- A `MainCameraSystem` will look for an entity that has the "MainCamera" component, and constantly copy its rotation to whatever Transform is assigned to `MainCameraSystem.CameraGameObjectTransform`.
- The `MainGameObjectCamera` monobehaviour can be added to a GameObject with a `Camera` component to automatically assign it to `MainCameraSystem.CameraGameObjectTransform`
- The `MainEntityCamera` component can be assigned to an Entity in a Subscene to mark it as the main camera on the Entities side
- The `OrbitCamera` component, `OrbitCameraInputs` component, and `OrbitCameraSystem` handle the orbit camera's logic and movement

And now here are the steps required to set up the camera in this tutorial scene:
- Add the "Simple Camera" files to your project (download link above)
- Create a new GameObject in the Subscene, and call it "OrbitCamera"
- Add an `OrbitCameraAuthoring` and `MainEntityCamera` to that "OrbitCamera" GameObject
- In the `OrbitCameraAuthoring` component, assign the character GameObject to the `FollowedCharacter` field. This represents which character object the camera will be following
* Optional: on the character GameObject, you can add a `CameraTarget` component to specify a child transform of the character that will act as the real target of the camera. Note that we still need the root character to be assigned to the `FollowedCharacter` field of the camera, because the `OrbitCameraSystem` needs to read some data from character components on that entity.
- Add a `MainGameObjectCamera` component to the GameObject that has Unity's `Camera` component

![](../Images/tutorial_camera_setup.png)

This should take care of setting up the camera. If you press Play now, you should have a camera that is centered on your character. However, nothing is controllable yet. We'll add input handling in the next section.
