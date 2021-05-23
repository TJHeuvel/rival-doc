Back to [How To](../how-to.md)

# Determine Character Grounding at a Given Point

If you need to know if your character would be grounded on a give hit, you can call your character processor's `IsGroundedOnHit` with that hit as parameter. Any physics hit can be turned into a `BasicHit` by using the `BasicHit` constructor: `var myBasicHit = new BasicHit(myHit);`. And you can leave the `groundingEvaluationType` to 0 (which represents "Default").

Note that if you change your `IsGroundedOnHit` implementation in a way that makes it depend on things such as character position or rotation, you will need to create a new function that determines grounding on hit at a given position/rotation. And your processor's `IsGroundedOnHit` should call that new function with the character's current `Translation` & `Rotation` as parameters

For example:
```cs
public struct MyCharacterProcessor : IKinematicCharacterProcessor
{
    // (...)

    // This is the default "IsGroundedOnHit" callback from the IKinematicCharacterProcessor interface
    public bool IsGroundedOnHit(in BasicHit hit, int groundingEvaluationType)
    {
        // Here we just call our new specific "IsGroundedOnHit" function
        return IsGroundedOnHitAtTransform(in hit, groundingEvaluationType, Translation, Rotation, GroundingUp);
    }
    
    // (...)

    // This is our new "IsGroundedOnHit" that takes a certain character translation/rotation/up as parameters
    public bool IsGroundedOnHitAtTransform(in BasicHit hit, int groundingEvaluationType, float3 atTranslation, quaternion atRotation, float3 atGroundingUp)
    {
        // Here we would do the real "IsGroundedOnHit" evaluation based on "atTranslation" and "atRotation"

        // (...) 
    }

    // (...) 

    // This is our character update loop
    public void OnUpdate()
    {
        // (...) 

        // in our character update, if we need to determine if the character would be grounded at a certain point here, we'd call:
        bool wouldBeGroundedAtPoint = IsGroundedOnHitAtTransform(new BasicHit(atHit), 0, atTranslation, atRotation, atGroundingUp);
    }
}
```