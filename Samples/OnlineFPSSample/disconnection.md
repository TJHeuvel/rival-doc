---
layout: default
title: Disconnection
parent: Online FPS Sample
grand_parent: Samples
nav_order: 5
---

# Disconnection
In `ClientGameSystem`, when we detect that our `NetworkIdComponent` representing the connection to the server has disconnected (when there is a `NetworkStreamDisconnected` on that connection entity), we simply reload the menu scenes

In `ServerGameSystem`, when we detect that a client connection has disconnected similarly, we go thorough all entities that have been registered as belonging to that connection in the `ConnectionOwnedEntity` DynamicBuffer on that connection entity, and we destroy them. That way, the Character and Player entities of disconnected clients get cleaned up.