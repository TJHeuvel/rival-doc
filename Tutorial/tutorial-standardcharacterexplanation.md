
Back to [Tutorial](../tutorial.md)

# Tutorial - Standard Character Overview

We will now take a moment to go over every part of the standard third-person character in order to gain a better understanding of it before we continue.

We have 3 main entities: 
- Player (`ThirdPersonPlayer`): Represents the human gathering input and controlling the Character & Camera
- Character (`ThirdPersonCharacter`): Represents the character that moves around in the world
- Camera (`OrbitCamera`): represents the camera that orbits around the character

The idea here is that the Player gathers input, transfers that input to components on the Character & Camera, and finally the Character & Camera move with that information. Let's take a closer look at how this happens in code:

## Player
1. `ThirdPersonPlayerSystem` gathers input from Unity's input system, and then iterates on all entities with a `ThirdPersonPlayer` component. 
1. For each one of those player entities, if it has a valid `player.ControlledCharacter`, we get the `ThirdPersonCharacterInputs` component on it and write inputs into that component. Notice that we don't directly write raw input in there; instead, we write inputs in a way that is easy for the character to understand. We convert our raw move input to a camera-relative worldspace vection, etc....
1. We do the same for the `player.ControlledCamera`; we write into the `OrbitCameraInputs` in order to tell the camera what to do

## Character
1. `ThirdPersonCharacterMovementSystem` updates at a fixed timestep, and schedules a `ThirdPersonCharacterJob` that handles all character movement & collision.
1. in the `ThirdPersonCharacterJob`, we build a `ThirdPersonCharacterProcessor` struct with all the data it needs, and call `OnUpdate()` on it. The character processor is essentially a single place where you implement the character movement logic and have access to all the data you need.
1. in the `ThirdPersonCharacterProcessor.OnUpdate()`, we have all the various steps required for character control. The part that you are most likely to want to modify & customize is `HandleCharacterControl()`. This is where you tell the character which velocity it should have right now.
1. the default code in `ThirdPersonCharacterProcessor.HandleCharacterControl()` reads from `ThirdPersonCharacterInputs` (the component our Player system was writing its inputs to), and controls the velocity accordingly

## Camera
1. `OrbitCameraSystem` reads from `OrbitCameraInputs` (the component our Player system was writing its inputs to), and moves the camera accordingly while always staying anchored to its `orbitCamera.FollowedCharacterEntity`


For more details on the standard character & camera setup, visit the following pages:
* [Third Person Character](../StandardCharacters/stdcharacters-thirdperson.md) 
* [DOTS Camera Setup](../StandardCharacters/stdcharacters-maincamera.md)