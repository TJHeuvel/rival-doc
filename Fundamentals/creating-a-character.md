---
layout: default
title: Creating a Character Controller
parent: Fundamentals
nav_order: 1
---

# Creating a Character Controller

In Rival, the recommended way to get started implementing your own character is by doing these steps:
- Generate the code for your new character by using the `Character Controller Wizard` window (Window > Rival > Character Controller Wizard)
- Add the generated character authoring component to a GameObject that has a PhysicsShape component
- Write a system that controls the inputs for the character (by writing to the generated character inputs component)
- Customize the character's implementation inside of the generated classes

The `Character Controller Wizard` is a window that allows you to specify the name, namespace (optional), and path of the generated character controller files. An info box gives you a preview of what exactly will be generated. Once you've entered a name, namespace, and path, click the "Generate" button to create the files. See [Character Code Structure](code-structure) for an overview of the generated files.

![](../Images/character-wizard.png)


