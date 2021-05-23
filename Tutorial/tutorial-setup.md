Back to [Tutorial](../tutorial.md)

# Tutorial - Project Setup

We will start by creating a project that is set-up for DOTS.

- Create/Open a Unity project using a Unity version that is verified to work with Rival. Make sure it also uses either the URP or HDRP (required for rendering in DOTS). 
- Go to "Edit > Project Settings > Player Settings", and enable "Allow 'unsafe' code". (Alternatively, if you want your code to be part of an .asmdef, make sure to enable unsafe code in that .asmdef)
- Go to "Edit > Project Settings > Package Manager", and enable "Allow Preview Packages"
- Import the Rival package. This will take care of importing the Entities and Unity.Physics packages into your project, if not already present.
- Import the "Hybrid Renderer" package as well. Click the "+" button at the top left of the package manager window, select "Add package from git URL", write `com.unity.rendering.hybrid`, and press "Add". This should import the package into your project
- Impport the "DOTS Editor" package as well. Follow the same instructions as for the previous step, but search for `com.unity.dots.editor` instead
- Go to "Edit > Project Settings > Player Settings", and add `ENABLE_HYBRID_RENDERER_V2` to the "Scripting Define Symbols" list. This enables the up-to-date features of the Hybrid Rendered
- (RECOMMENDED) Go to "Edit > Project Settings > Editor". Enable "Enter Play Mode Options", and make sure the "Reload Domain" and "Reload Scene" underneath are both disabled. This will make entering play mode much faster
- (RECOMMENDED) In the top bar menu: Make sure that "Jobs > Burst > Enable Compilation" is enabled, "Jobs > JobsDebugger" is disabled, and "Jobs > Leak Detection" is **not** set to "Full Stack Traces". Not doing so will have a significant performance impact when playing in editor