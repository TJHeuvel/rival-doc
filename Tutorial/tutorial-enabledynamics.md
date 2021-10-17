Back to [Tutorial](../tutorial.md)

# Tutorial - Enable Dynamics

Rival supports simulating a dynamic body for characters, which means the character can push & be pushed by other dynamic bodies with a given mass.

In order to enable dynamic mode on the character, there are three things to do:
- In your character authoring component inspector, set `Simulate Dynamic Body` to true
- In your character authoring component inspector, set `Mass` to the desired mass (we can just keep 1 for now)
- Set the character's `CollisionResponse` in the `PhysicsShape` component to "Raise Trigger Events". This is required because the other dynamic bodies that come into contact with the characters must not actually generate contacts with the character collider. We must let the character update code solve those contacts in an entirely custom manner.

Next, we need to add a dynamic box to the scene, in order to push it with our character:
- In your Subscene, make sure you have a GameObject with the `PhysicsStep` component on it. This enables DOTS physics simulation [[Screenshot]](../Images/tutorial_physicsstep.png)
- Add a cube to your Subscene, and add a `PhysicsBody` and `PhysicsShape` to it, correctly set-up for being a dynamic body with Interpolation [Screenshot]](../Images/tutorial_physicsbox.png)

Press Play, and try to push it. Play with the masses of the character and the box to see how different mass ratios affect the pushing.

![](../Images/tutorial_enable_dynamic.gif)

For more details on dynamic mode for characters, see [Dynamic Body Interaction](../How_To/dynamic-body-interaction.md)


## SynchronizeCollisionWorld

By default, the physics systems don't update the `CollisionWorld` after the physics step has moved bodies with their velocity. Since our character updates after physics, and relies on the `CollisionWorld` in order to detect hits, leaving things like this may result in a slight visual lag when a character pushes or gets pushed by another body. 

We can solve this by enabling the `SynchronizeCollisionWorld` option in our `PhysicsStep`'s inspector. This will take care of updating the `CollisionWorld` properly, and get rid of any lag. 