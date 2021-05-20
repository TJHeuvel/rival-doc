# Getting Started

![](./Images/logo.png)

You can get Rival on the Unity Asset Store: [Link](https://assetstore.unity.com/packages/slug/195567)

This package is verified for Unity 2020.3, and it is recommended that you use only that version for the time being. This is the version recommended by Unity for DOTS development, until further notice.

In Rival, the recommended way to get started implementing your own character is by doing these steps:
- Generate the code for your new character by using the **Character Controller Wizard** window. 
- Add the generated character authoring component to a GameObject.
- Customize the character's implementation inside of the generated files, and/or in extra systems.


### Character Controller Wizard

The **Character Controller Wizard** is a window that allows you to generate all the files necessary for the creation of a new character controller. It can be accessed through Unity's top bar menu: **"Window > Rival > Character Controller Wizard"**. 

You can specify the name, namespace (optional), and path of the generated character controller files, and an info box gives you a preview of what exactly will be generated. Once you've entered a name, namespace, and path, click the "Generate" button to create the files.

![](./Images/character_wizard.png)


### Character Implementation Overview

Assumming we chose "MyCharacter" as the character name in the **Character Controller Wizard**,

#### Character Components
The `MyCharacterAuthoring` component adds all of the required character components to the entity during conversion. Mainly:
- `KinematicCharacterBody`: contains the core data common to all characters (velocity, grounding, movement parameters, etc...).
- `MyCharacterComponent`: contains the data that is specific to your own implementation.
- `MyCharacterInputs`: contains inputs to the character movement, like the desired character movement direction and jump requests. It acts as a common interface for both human players & AI systems to control the character. 

#### Character System
The `MyCharacterSystem` schedules a `MyCharacterJob` that iterates on the components of your character. For each character, the `MyCharacterJob` creates a temporary `MyCharacterProcessor` struct, initializes all of its data, calls `MyCharacterProcessor.OnUpdate`, and finally writes back updated data from the `MyCharacterProcessor` to the components.

#### Character Processor

`MyCharacterProcessor.OnUpdate` calls all of the major character update steps. These steps handle initializing the character values for the frame, handling parent movement, detecting ground, moving with collisions, detecting moving platforms, etc... Some of them are optional (see the comments in the code), but they must always be called in that order if they are called.

`MyCharacterProcessor.HandleCharacterControl` contains basic character control logic, and you are free to customize or replace this entirely with your own implementation. This function typically has 2 main goals: controlling the `KinematicCharacterBody.RelativeVelocity` of the character, and controlling the `Rotation` of the character. The default implementation basically does this:
```cs
// if the character is grounded...
    // interpolate velocity towards input direction for responsive ground control
    // add jump velocity if jump input is requested
// otherwise, if the character is not grounded...
    // accelerate velocity towards input direction for air control
    // add gravity
    // apply drag

// rotate the character towards its move direction (if there is any move input)

// reset the jump input request
```

Notice that `KinematicCharacterBody` contains important information about grounding.

`MyCharacterProcessor` also contains several "processor callbacks" received from the internal character update loop. This is where you can customize things like what the character can collide with, whether the character is grounded on a hit or not, how the velocity is projected, etc... but you can keep the default implementation.


## Useful Links

See [Tutorial](./tutorial.md) for a guided step-by-step tutorial on creating and customizing a new character from scratch.

See [Samples](./samples.md) for a sample project containing various games and test scenes made using Rival.

See [Core](./core.md) for an in-depth overview of the core components & systems. 

See [How To](./how-to.md) for explanations on how to implement various character features.

See [Tips](./tips.md) for a list of particularities to be aware of, and performance tips.
