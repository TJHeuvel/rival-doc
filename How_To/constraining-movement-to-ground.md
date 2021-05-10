

# Constraining Movement to the Ground Plane

The default code-generated character's movement is constrained to the ground plane, which means that it will not bump into steep slopes that exceed its `KinematicCharacterBody.MaxGroundedSlopeAngle`.
 
If you want to disable this behaviour, you will have to change something in your character's `IKinematicCharacterProcessor.ProjectVelocityOnHits`. By default, this function makes a call to a default velocity projection method. If you simply set the `constrainToGoundPlane` parameter of the `KinematicCharacterUtilities.DefaultProjectVelocityOnHits` function to false, your character will now be able to "bump" onto steep slopes and be ungrounded when it walks towards them, instead of being stopped instantly and remaining grounded.