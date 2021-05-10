

# Navmesh Integration

todo: waiting for further updates on DOTS navmeshes

The general idea for making characters use navmeshes is to do the following:
- Calculate a path on the navmesh using the navmesh API, using an agent size that is similar to your character size
- Have a system that controls your character's velocity to make it go towards the next point in the path
- Keep track of when you are close enough to a point in the path to determine that you have reached it, and then keep moving towards the next point in the path