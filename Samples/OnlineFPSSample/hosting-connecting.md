Back to [Samples](../../samples.md)

# OnlineFPS Sample - Hosting & Connecting

In order to play the OnlineFPS, you need to either host a game, or connect to a game. This is done through the menu in the "OnlineFPSMenu" scene.

![](../../Images/onlinefps-connection-menu.png)


## Building the project

It is recommended that you build the game before you try playing online. To build the game, navigate to the "BuildConfig" folder under the "Sample_OnlineFPS" folder, find the config named "OnlineFPSClientBuildConfig", and press "Build" in the top right. Despite its name, the "OnlineFPSClientBuildConfig" is also able to host games. "OnlineFPSServerBuildConfig" is for when you want a server-only build that is in headless (no rendering) mode.

NOTE: At the time of writing this, there is a bug with the HybridRenderer causing the lightmaps not to appear in builds unless the scene containing the lightmaps is currently open in editor at the time of building. So before you build, open the "OnlineFPS" scene in the editor.


## Hosting a game

To host a game, you need to specify a port on which to listen to connections ("Host Port"), and then click the "Host Game" button. This will start a server for the game, as well as a local client for you to play as.

NOTE 1: When hosting a game, if you want others to be able to connect, it is important that you **forward the port** you specified as you "Host Port" in your router settings. The exact procedure varies depending on the router. If you don't know how to do it, search for "port forwarding" + the brand of router you have.

NOTE 2: If people still can't connect to your game even after forwarding the port, consider allowing the build .exe through the firewall, of temporarily deactivating your firewall.


## Connecting to a game

To connect to a game/server, you must enter the IP and port of the desired server in the "Join IP" and "Join Port" fields. Once this is done, press the "Join Game" button.

The "Join IP" is the public IP of the server. You can get the public IP by going to https://www.whatismyip.com/ on the computer that is hosting the game.

The "Join Port" is the port that the server specified as their "Host Port".