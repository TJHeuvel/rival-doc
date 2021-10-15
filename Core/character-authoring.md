Back to [Core](../core.md)

# Character Authoring & Entity Structure

When using [Standard Characters](/standard-characters.md), the character authoring components will call `KinematicCharacterUtilities.HandleConversionForCharacter`, which handles setting up the character entity correctly with all the required components. 

The core components added to the character entity during conversion are:
- `KinematicCharacterBody`
- `PhysicsVelocity`
- `PhysicsMass`
- `PhysicsGravityFactor`
- `PhysicsCustomTags`
- `CharacterInterpolation` (optional depending on configuration)
- `DynamicBuffer<KinematicCharacterHit>`
- `DynamicBuffer<KinematicVelocityProjectionHit>`
- `DynamicBuffer<KinematicCharacterDeferredImpulse>`
- `DynamicBuffer<StatefulKinematicCharacterHit>`

On top of these core components, the character authoring components of standard characters will also add:
- A character-specific data component (ex: `FirstPersonCharacterComponent`)
- A character-specific inputs component (ex: `FirstPersonCharacterInputs`)
