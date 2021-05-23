Back to [How To](../how-to.md)

# Root Motion Movement

todo: waiting for further updates on DOTS animation

The general idea for making characters move using root motion is to do the following:
- Obtain the position delta that would be caused by animation root motion on this frame
- Keep accumulating those position deltas on every frame, until we've reached a fixed update (where the character updates)
- Convert that position delta to a velocity (`float3 velocityFromRootMotion = positionDelta / deltaTime`)
- Assign that velocity to the character, and reset the accumulated position delta to zero