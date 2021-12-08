
Back to [Tutorial](../tutorial.md)

# Tutorial - Standard Character Setup

We will now go through the process of creating a new basic character, using the "StandardCharacters_ThirdPerson" package.

Start by double-clicking the `Assets/Rival/StandardCharacters/StandardCharacters_ThirdPerson` package, and importing it into your new project

![](../Images/tutorial_exportcharacter.png)

The new character files will be under the "Rival_StandardCharacters" folder in your project. At this point, you could choose to move the files into other folders, and rename the classes/structs/prefabs to your liking. However, for the remainder of tutorial, will will use the default names.

Next, we will create a basic scene containing a rudimentary environment for our character to be in. Create a new scene, and add the following objects to it:
- a Main Camera (the default one will do)
- a Directional Ligght (the default one will do)
- A Subscene containing floors and walls (using the PhysicsShape and PhysicsBody components for DOTS physics)

![](../Images/tutorial_basicscene.png)

We will now add our character to the scene and set up its references.
* Add the `ThirdPersonCharacter`, `ThirdPersonPlayer`, and `OrbitCamera` prefabs to your Subscene [[Screenshot]](../Images/stdcharacters-tp1.png)
* Assign the `ThirdPersonCharacter` to the `ControlledCharacter` field of `ThirdPersonPlayer`, and assign the `OrbitCamera` to the `ControlledCamera` field of `ThirdPersonPlayer` [[Screenshot]](../Images/stdcharacters-tp2.png)
* Assign the `ThirdPersonCharacter` to the `FollowedCharacter` field of `OrbitCameraAuthoring` [[Screenshot]](../Images/stdcharacters-tp3.png)
* Add a `MainEntityCameraAuthoring` component to the `OrbitCamera` object [[Screenshot]](../Images/stdcharacters-tp5.png)
* Add a `MainGameObjectCamera` component to the `Main Camera` gameobject (this is the one that's not in a subscene) [[Screenshot]](../Images/tutorial_camera_setup.png)

If you try pressing Play now, you should be able to control your new character

![](../Images/tutorial_firstplay.gif)

For more details on the standard character & camera setup, visit the following pages:
* [Third Person Character](../StandardCharacters/stdcharacters-thirdperson.md) 
* [DOTS Camera Setup](../StandardCharacters/stdcharacters-maincamera.md)