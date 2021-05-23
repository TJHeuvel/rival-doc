Back to [How To](../how-to.md)

# Ground Pushing

Pushing bodies that you are grounded on is a special case, due to several complexities & particularities related to character grounding. It will not be handled simply by enabling `KinematicCharacterBody.SimulateDynamicBody`.

Instead, it is handled by calling the `KinematicCharacterUtilities.DefaultMethods.UpdateGroundPushing` function in your character update. The default code-generated character handles this in its `MyCharacterProcessor.OnUpdate`. You can study what this function does internally, and customize it to your liking (or remove it).