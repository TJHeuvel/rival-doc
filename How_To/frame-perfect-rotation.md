

# Frame-Perfect Rotation
The default code-generated character update job, which updates at a fixed timestep, may not always be the ideal place to handle character rotation. It's fine when the character rotates independantly from the camera, but in games like FPSs where the character rotation & camera must be synced 1:1, problems could arise. 

In those cases, it is recommended to handle character rotation in a separate system that updates at a variable timestep (in the `SimulationSystemGroup`), instead of in a fixed step system. Doing so will give you perfectly smooth and responsive rotations, because the camera updates at a variable timestep as well, and therefore the character and camera will be in perfect sync. 