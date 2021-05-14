
# Samples - How to Install

Here are the steps required in order to install the Rival Samples project:

**WARNING: this will completely overwrite the project settings and dependencies. Make sure you do this in a new empty project!**

1. Create a completely new Unity project, using the “3D” template. You can call this project “RivalSamples” for example. The starting template we choose here does not matter much, because all of the project settings will be overwritten by the Samples unitypackage later.

![](../Images/samples-install-createproject.png)

2. Import the Rival package into this project. 
3. A popup will appear saying this package has dependencies: click "Install/Upgrade"

![](../Images/samples-install-dependencies.png)

4. Wait for the importing/compilation to finish 
5. A popup will let you import the Rival files. Make sure all files are selected, and click "Import"

![](../Images/samples-install-import-rival.png)

6. Wait for the importing/compilation to finish 
7. In “Edit > Project Settings > Player”, set the “Active Input Handling” to “Both”. 

![](../Images/samples-install-input-both.png)

8. A popup will appear saying the editor must be restarted. Click "Apply" to restart (it is important to restart now before going any further)

![](../Images/samples-install-input-both-restart.png)

9. After the restart, go to the “Assets/Rival/Samples” folder, and open “RivalSamples.unitypackage” by double-clicking on it inside Unity's "Project" window. 
10. A popup will appear saying this package will overwrite all project settings: click "Import"

![](../Images/samples-install-overwrite-settings-popup.png)

11. A popup will appear saying this package has dependencies: click "Install/Upgrade"

![](../Images/samples-install-dependencies.png)

12. A popup will let you import the RivalSamples files. Make sure all files are selected, and click "Import"

![](../Images/samples-install-import-rival-samples.png)

13. Wait for the importing/compilation to finish 
14. At this point, restart Unity
15. Once restarted, you can find all samples under the “Rival_Samples” folder, and you're done!

Note: If you opened “RivalSamples.unitypackage”, and the editor restarted, and you don’t see the “Rival_Samples” folder once it has restarted, simply try to reopen the “RivalSamples.unitypackage” again, and do the procedure again. This is most likely due to the editor having to restart for certain new project settings, and not giving you the opportunity to import the assets the first time.

