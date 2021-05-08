---
layout: default
title: Input Handling
parent: How-To
nav_order: 1
---

# Input Handling

See the tutorial section on Input Handling for an example 

[Tutorial - Input Handling](../Tutorial/tutorial-input)


## Fixed Step Input
There is one important particularity to keep in mind when dealing with input for a character controller that updates at a fixed time step: punctual input presses (like detecting when a key was just pressed down) may get lost between two fixedstep updates, since they are usually processed at a variable time step.

The solution, for properly detecting those punctual input presses, is to remember if they have happened at least once since the last time we did a fixedstep update.