

# Determine Character Grounding at a Given Point

If you need to know if your character would be grounded at a given point, simply obtain a `RaycastHit`, `ColliderCastHit`, or `DistanceHit` of the point that you want to evaluate, turn it into a `BasicHit` using `var myBasicHit = new BasicHit(myHit);`, and finally call your character processor's `IsGroundedOnHit` with that hit as parameter. You can leave the `groundingEvaluationType` to 0