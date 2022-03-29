Back to [Tutorial](../tutorial.md)

# Tutorial - Project Setup

We will start by creating a project that is set-up for DOTS.

- Create/Open a Unity project using a Unity version that is verified to work with Rival. Make sure it also uses either the URP or HDRP (required for rendering in DOTS). 
- Go to `Edit > Project Settings > Player Settings`, and enable `Allow 'unsafe' code`. (Alternatively, if you want your code to be part of an .asmdef, make sure to enable unsafe code in that .asmdef)
- Go to `Edit > Project Settings > Package Manager`, and enable `Allow Preview Packages`
- Import the Rival package. This will take care of importing the `Unity.Entities` and `Unity.Physics` packages into your project, if not already present.
- Import the `Hybrid Renderer` package as well. Click the "+" button at the top left of the package manager window, select `Add package from git URL`, write `com.unity.rendering.hybrid`, and press "Add". This should import the package into your project
- (RECOMMENDED) Go to `Edit > Project Settings > Editor`. Enable `Enter Play Mode Options`, and make sure the `Reload Domain` and `Reload Scene` underneath are both **disabled**. This will make entering play mode much faster
- (RECOMMENDED) In the top bar menu: Use these settings to avoid in-editor performance issues. You can change them only when needed:
    - `Jobs > Burst > Enable Compilation` is enabled
    - `Jobs > JobsDebugger` is disabled
    - `Jobs > Leak Detection` is set to either "On" or "Off" (**not** `Full Stack Traces`)