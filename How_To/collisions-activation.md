

# Character Collisions Activation

Completely deactivating the collision handling of a character requires more than simply setting the character's PhysicsCollider's collision response to "None". Setting the collision response to "None" prevents other rigidbodies from colliding with the character, but it does not prevent the character from detecting its own collisions with physics queries.

In order to completely deactivate character collisions, you also need to disable the character's collision detection logic. There is a function you can call in order to disable/enable this easily: `KinematicCharacterBody.SetCollisionDetectionActive`