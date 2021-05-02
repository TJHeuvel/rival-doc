---
layout: default
title: Input Handling
parent: How-To
nav_order: 1
---

# Input Handling
There is one important particularity to keep in mind when dealing with input for a character controller that updates at a fixed time step: punctual input presses (like detecting when a key was just pressed down) may get lost between two fixedstep updates, since they are usually processed at a variable time step.

The solution, for properly detecting those punctual input presses, is to remember if they have happened at least once since the last time we did a fixedstep update.

A `FixedStepButton` struct is provided to help with these sorts of situations. In most of the sample projects, the characters use a `FixedStepButton` to detect jump presses, for example. You can use them by calling `myFixedStepButton.UpdateWithValue(inputValue, fixedStepUpdateNb)`, where "inputValue" is the boolean value of if the input was pressed down, and where "fixedStepUpdateNb" is the number of fixedstep updates that have run so far.