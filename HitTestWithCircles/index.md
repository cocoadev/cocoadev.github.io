---
layout: page
title: HitTestWithCircles
---



Hi everyone... This question is more math oriented than Cocoa oriented, but I think it's still pertinent...

I'd like to know if anyone has an idea of how a collision is detected between two balls is done, mathematically speaking.
And also how I can calculate their new trajectory after impact.
In my specific case, if the balls are both moving, they have the same mass.
And most of the time, it's a ball hitting a fixed round object.

-- Trax
----

From the sounds of it, it looks like you are asking about Spheres and not Circles? Either way, implementations of this type of code has to be rampant across the internet. How true do you need the physics?

----

To address just the page name, hit testing a point with a circle or two circles is easy.

A point is inside a circle if the distance between that point and the circle's center is less than the circle's radius.

Two circles intersect if the distance between their centers is less than the sum of their radii.

----

The above is absolutely correct.

That said, if you're interested in seeing an implementation, see NeHE lesson 30 for a fairly straightforward implementation which also supports arbitrary rectangular solids and planes. 

http://nehe.gamedev.net/data/lessons/lesson.asp?lesson=30

If you're concerned about physics, check out the Open Dynamics Engine. It does spheres, as well as cylinders, and boxes with inertia tensors and realistic physical response. For vanilla collision detection but no physics, it also supports rays, infinite planes, and arbitrary polygon soups. http://www.ode.org

--ShamylZakariya

----

I think you all gave me some useful tips. I still have some doubts however. I read that document about simple collision between a ball (or sphere) and a plane :
http://www.gamedev.net/reference/articles/article1026.asp

At the beginning, it reads :
    
// Inputs: plane origin, plane normal, ray origin ray vector.
// NOTE: both vectors are assumed to be normalized

double intersect(Point pOrigin, Vector pNormal, Point rOrigin, Vector rVector)
{
   double d = -(planeNormal * planeOrigin);
   double numer = planeNormal * rayOrigin + d;
   double denom = planeNormal * rayVector;
   return -(numer / denom);
}

I'm not sure what exactly is a normalized vector. And I'm wondering how is a vector represented in programming terms. A struct, I guess ?

-- Trax

----

A normalized vector is one which has been scaled so that it's length one.  If v is a vector, v/|v| is normalized.

Plus, that code seems to be for a plane and a ray, not a plane and a sphere.

Lastly, that looks like C++, so a Vector and a Point are objects.  And dang if that isn't a good argument against abusive operator overloading.  Vector*Vector?  Dot product, cross product or other?  Vector*Point?  I don't even know.  

Someone else can come and say why I'm being silly.

----

A point and a vector can be looked at as the same thing. (Think of a point as just the vector from the origin to the point.) So doing point cross vector or point dot vector makes perfect sense. Since the results are being saved into scalars, it's clearly a dot product.

Still, I had to actually *look* and see what the result type was, so I agree that it's a bad use of overloading.

