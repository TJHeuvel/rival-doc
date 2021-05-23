Back to [How To](../how-to.md)

# Camera Handling

See the tutorial section on Camera Handling for an example 

[Tutorial - Camera Handling](../Tutorial/tutorial-camera.md)


## Dealing with character interpolation

The camera should always follow the interpolated character transform; not just the raw Translation/Rotation component values. Following the non-interpolated character transform will often result in jitter.

The interpolated character transform values are found in the `LocalToWorld` component, and are calculated in the `TransformSystemGroup` For this reason, any character camera system should always update after the `TransformSystemGroup`