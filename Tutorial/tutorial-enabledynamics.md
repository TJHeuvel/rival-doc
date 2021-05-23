Back to [Tutorial](../tutorial.md)

# Tutorial - Enable Dynamics

Rival supports simulating a dynamic body for characters, which means the character can push & be pushed by other dynamic bodies with a given mass.

In order to enable dynamic mode on the character, there are three things to consider:
- In your character authoring component inspector, set `SimulatedDynamic` to true
- In your character authoring component inspector, set `Mass` to the desired mass
- Set the character's `CollisionResponse` in the `PhysicsShape` component to "Raise Trigger Events". This is required because the other dynamic bodies that come into contact with the characters must not actually generate contacts with the character collider. We must let the character update code solve those contacts in an entirely custom manner.

Add a dynamic box to the Subscene, press Play, and try to push it. Play with the masses of the character and the box to see how different mass ratios affect the pushing.

![](../Images/tutorial_enable_dynamic.gif)

For more details on dynamic mode for characters, see [Dynamic Body Interaction](../How_To/dynamic-body-interaction.md)


## SynchronizeCollisionWorld

By default, the physics systems don't update the `CollisionWorld` after the physics step has moved bodies with their velocity. Since our character updates after physics, and relies on the `CollisionWorld` in order to detect hits, leaving things like this may result in a slight lag when a character pushes or gets pushed by another body. 

We can solve this by adding a new empty GameObject to our Subscene, calling it "PhysicsStep", and adding a `PhysicsStep` component to it. Then, enable `PhysicsStep.SynchronizeCollisionWorld` in the inspector. This will take care of updating the `CollisionWorld` properly, and get rid of any pushing lag. 