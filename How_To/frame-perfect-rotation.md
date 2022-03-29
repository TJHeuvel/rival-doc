Back to [How To](../how-to.md)

# Frame-Perfect Rotation

The standard characters handle their rotation and translation movement in different systems. Ex: `FirstPersonCharacterMovementSystem` and `FirstPersonCharacterRotationSystem`

The "MovementSystem" updates at a fixed timestep, while the "RotationSystem" updates at a variable timestep. This is because it is highly desirable to have a rotation that feels as frame-perfect & responsive as possible, especially when controlling with the mouse. Translation movement, on the other hand, is more forgiving of small input delays, and needs to update at a fixed timestep in order to properly interact with physics

This represents the recommended setup for ideal character movement:
- Update character velocity at a fixed timestep (in `KinematicCharacterUpdateGroup`)
- Update character rotation at a variable timestep (in `SimulationSystemGroup`) 