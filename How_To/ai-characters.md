---
layout: default
title: AI Characters
parent: How-To
nav_order: 13
---

# AI Characters
There is no *correct* way of handling AI characters, but there is a pattern that I like to use frequently and that I can recommend.

The idea is to make it so that both player characters and AI characters use the exact same character movement system, so that the same movement rules apply to all of them without relying on copy/pasting code between a "PlayerCharacterSystem" and a "AICharacterSystem". We can achieve this by making our character system rely on a set of character inputs that are common to both player character and AI characters.

We end up with this sort of structure:
- have a MyCharacterSystem that reads from a MyCharacterInputs component and moves based on those input
- MyCharacterInputs would have, for example, a MoveVector field and a LookDirection field
- for player characters, a system will read keyboard/gamepad input and convert those inputs to a "MoveVector" and "LookDirection" in the MyCharacterInputs component
- for AI characters, an AI system would calculate a character's desired movement, and write to the "MoveVector" and "LookDirection" fields of MyCharacterInputs accordingly

This way, we have both player and AI characters using the exact same logic. They simply have different controlling systems that calculate their inputs.