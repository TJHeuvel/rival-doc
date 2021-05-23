Back to [How To](../how-to.md)

# Prevent Sliding Along Walls Within a Certain Angle Threshold

When your character is moving against a wall at a very perpenticular angle, you may want to prevent it from sliding sideways. Or you may want to simulate a friction with the wall to slow down the character velocity. This can best be accomplished through the `ProjectVelocityOnHits` callback of your character's processor.

The idea is to let the default projection happen in order to obtain the regular solved sliding velocity, but if we detect that we were moving against a wall at a very perpenticular angle, we project the velocity to keep only the vertical part of it:
```cs
public void ProjectVelocityOnHits(
    ref float3 velocity,
    ref bool characterIsGrounded,
    ref BasicHit characterGroundHit,
    in DynamicBuffer<KinematicVelocityProjectionHit> hits,
    float3 originalVelocityDirection)
{
    // Remember velocity before it was projected
    float3 velocityBeforeProjection = velocity;

    // Call the regular velocity projection
    KinematicCharacterUtilities.DefaultMethods.ProjectVelocityOnHits(
        ref velocity,
        ref characterIsGrounded,
        ref characterGroundHit,
        in hits,
        originalVelocityDirection,
        GroundingUp,
        BasicCharacter.ConstrainVelocityToGroundPlane);

    // if the latest hit was not-grounded and was within a certain angle threshold with our original velocity (that threshold is calculated with the dot product)...
    KinematicVelocityProjectionHit latestHit = hits[hits.Length - 1];
    if (!latestHit.IsGroundedOnHit && math.dot(latestHit.Normal, math.normalizesafe(velocityBeforeProjection)) < -0.85f)
    {
        // ...project the final velocity onto the up vector, to keep only the vertical part of it
        velocity = math.projectsafe(velocity, GroundingUp);
    }
}
```

Note: velocity projection works with a collection of all hits so far in the frame in order to better deal with creases and corners. But the last hit in the `hits` buffer represents the most recent hit.

Without this feature:

![](../Images/preventwallslide-before.gif)

With this feature:

![](../Images/preventwallslide-after.gif)

_________________________________

If you don't want to completely stop the velocity horizontally, but instead simulate a certain friction/slowdown when the character moves against a wall, you could do something like this instead: 
```cs
public void ProjectVelocityOnHits(
    ref float3 velocity,
    ref bool characterIsGrounded,
    ref BasicHit characterGroundHit,
    in DynamicBuffer<KinematicVelocityProjectionHit> hits,
    float3 originalVelocityDirection)
{
    // Call the regular velocity projection
    KinematicCharacterUtilities.DefaultMethods.ProjectVelocityOnHits(
        ref velocity,
        ref characterIsGrounded,
        ref characterGroundHit,
        in hits,
        originalVelocityDirection,
        GroundingUp,
        BasicCharacter.ConstrainVelocityToGroundPlane);

    // if the latest hit was not-grounded...
    KinematicVelocityProjectionHit latestHit = hits[hits.Length - 1];
    if (!latestHit.IsGroundedOnHit)
    {
        // Reduce the final solved velocity
        velocity *= 1f - frictionRatio;
    }
}
```