# Getting Started

![](./Images/logo.png)

You can get Rival on the Unity Asset Store: [Link](https://assetstore.unity.com/packages/slug/195567)

This package is verified with Unity 2020.3, and it is recommended that you use only that version.

In Rival, the recommended way to get started implementing your own character is by doing these steps:
- Generate the code for your new character by using the **Character Controller Wizard** window. 
- Add the generated character authoring component to a GameObject.
- Customize the character's implementation inside of the generated files.


### Character Controller Wizard

The **Character Controller Wizard** is a window that allows you to generate all the files necessary for the creation of a new character controller. It can be accessed through Unity's top bar menu: **"Window > Rival > Character Controller Wizard"**. 

You can specify the name, namespace (optional), and path of the generated character controller files, and an info box gives you a preview of what exactly will be generated. Once you've entered a name, namespace, and path, click the "Generate" button to create the files.

![](./Images/character_wizard.png)


### Character Implementation Overview

Assumming we chose "MyCharacter" as the character name in the **Character Controller Wizard**,

The `MyCharacterAuthoring` component adds all of the required character components to the entity during conversion. Mainly:
- `KinematicCharacterBody`: contains the core data common to all characters (velocity, grounding, movement parameters, etc...).
- `MyCharacterComponent`: contains the data that is specific to your own implementation.
- `MyCharacterInputs`: contains things like the desired character movement direction and jump requests, and is meant to be written-to by player input or AI systems.

Implementing your custom character movement logic is mostly done in the `MyCharacterProcessor`. This struct is created by the `MyCharacterSystem.MyCharacterJob` for each character during the job's update loop, and the job calls `MyCharacterProcessor.OnUpdate`. The roles of the `MyCharacterProcessor` are:
- Containing all the data required for a character's update
- Implementing the `MyCharacterProcessor.OnUpdate` function. This is where all the major character update steps are called, and `MyCharacterProcessor.HandleCharacterControl` is where the basic default character movement is implemented (you are free to customize or completely replace the default implementation). Character movement is mostly a question reading from `MyCharacterInputs`, and using those inputs to control the `KinematicCharacterBody.RelativeVelocity` and the `Rotation` of the character. You can also access grounding information via `KinematicCharacterBody.IsGrounded` and `KinematicCharacterBody.GroundHit`. You can study the default `MyCharacterProcessor.HandleCharacterControl` implementation to learn the basics.
- Implementing "processor callbacks" from the internal character update loop. This is where you can customize things like what the character can collide with, whether the character is grounded on a hit or not, how the velocity is projected, etc...

However, nothing forces you to implement **all** of the character logic in `MyCharacterProcessor`. It is simply there to give you easy access to the exposed character update loop, as well as all the data that the character update already requires, but you are allowed to create separate systems that handle other aspects of the character movement. For example, a separate "wind zone" system could detect trigger events with a character, and add to that character's velocity to simulate a wind push force.


## Useful Links

See [Tutorial](./tutorial.md) for a guided step-by-step tutorial on creating and customizing a new character from scratch.

See [Samples](./samples.md) for a sample project containing various games and test scenes made using Rival.

See [Character Code Structure](./Core/code-structure.md) for an in-depth overview of the generated files. 

See [Kinematic Character Body](./Core/kinematic-character-body.md) for an overview of the various fields & parameters contained in the `KinematicCharacterBody` component.

See [How To](./how-to.md) for explanations on how to implement various character features.

See [Tips](./tips.md) for a list of particularities to be aware of, and performance tips.
