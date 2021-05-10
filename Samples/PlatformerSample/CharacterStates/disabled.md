

[Back to Character](../character.md)

# Platformer Sample - Disabled

The `DisabledState` has no movement handling, and simply handles turning off character collisions in its `OnStateEnter`, and turning them back on in its `OnStateExit`.

The `DisabledNoCollisionsState` variant is similar except that it also changes the character collider's `CollisionResponse`.