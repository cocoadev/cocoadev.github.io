---
layout: page
title: RemovingACAConstraintFromACALayer
---

I was wondering if there was a way to remove / modify a CAConstraint I added to a CALayer somewhere in code before.
I think it's weird there's a -addConstraint method for CALayer, but no -removeConstraint.

Maybe setting the "same" constraint with a different offset might work?
So, before I have something like:
    
[myCALayer addConstraint:[CAConstraint addConstraintWithAttribute:kCAConstraintMidY relativeTo:@"superlayer" attribute:kCAConstraintMidY offset:20]];

and then I later write this for the same layer:
    
[myCALayer addConstraint:[CAConstraint addConstraintWithAttribute:kCAConstraintMidY relativeTo:@"superlayer" attribute:kCAConstraintMidY offset:0]];

and it recognizes that it's for the same attribute (kCAConstraintMidY) and does not ADD but REPLACE this constraint for that attribute...?

Any ideas?

----
Please use the forums for questions like these.

