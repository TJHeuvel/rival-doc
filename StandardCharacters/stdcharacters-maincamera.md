
# Standard Characters - Main Camera

Since Cameras can't be converted yet in DOTS, we have to do a little workaround to let us implement camera controls in components & systems. What we want to do is to keep a regular Unity GameObject Camera, but tell it to copy the transform of a designated Entity every frame. Here's how you can do this with the scripts included under the "MainCamera" folder of the Standard Characters:
* Have a regular GameObject camera in your scene (not in a subscene)
* Add a `MainGameObjectCamera` to that camera GameObject. This will tell the `MainCameraSystem` that this is the camera we want to use for rendering
* Add a `MainEntityCamera` to a Subscene GameObject that is meant to represent the controllable camera entity.

Every frame, the `MainCameraSystem` will then copy the position/rotation of the `MainEntityCamera` to the `MainGameObjectCamera`

Also see: [Tutorial - Camera Setup](../Tutorial/tutorial-camera.md) 
