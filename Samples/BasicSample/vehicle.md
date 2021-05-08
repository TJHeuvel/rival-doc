---
layout: default
title: Vehicle
parent: Basic Sample
grand_parent: Samples
nav_order: 5
---

# Vehicle

The vehicle logic is all in the `VehicleSystem` It uses cylinder collider casts to detect wheel collisions and apply suspension forces to the vehicle physics body accordingly. For the sake of simplicity, the input-handling for the vehicle is simply hard-coded in the `VehicleSystem` class.