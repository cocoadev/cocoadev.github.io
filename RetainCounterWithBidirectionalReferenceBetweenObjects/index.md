---
layout: page
title: RetainCounterWithBidirectionalReferenceBetweenObjects
---

just wonder what a good way to handle bidirectional aggregation between two objects is under objective-c's retain counter system Here's an example of what i mean (i'ved used a psudeo-c++ example as it is less typing):
    
class A
{
   A *parent;
   A * child;

   void setParent(A*aNode)
   {
       parent = [aNode retain];
   } 

   void setChild(A*aNode) 
  {
      child = [aNode retain];
      [aNode setParent: self];  
  }
}

//usage
{
   A * a = A alloc] init];
   B * b = [[B alloc] init];    //at this point ref count of a=1 and ref count of b=1

   [a setChild: b]   //at this point ref count of a=2 and ref count of b=2
   //where done so release
   [a release];
   [b release];  //at this point ref count of a=1 and ref count of b=1
                         //but objects a and b are left dangling with a reference to each other so
                         //they'll never be deleted
}


If we look at it more closely, circular reference will cause this:   a references b, b references c, c references..n,..n references a  So we get a sort of deadlock here, since each object is waiting for the next to release.  This is a problem that is inherent to the retain count system, but i'm just wondering a good solution to solve this problem is.

Should i have a destroy method that will force the destructor call (by reducing this instances ref count to zero), could i have a method that destroyed the relationship between these two objects?  Should i not call one of the retains in my set parent method?...  Each of these solutions, from what i see have there problems, and none is a clean solution.  With the first two, someone would be responsible for calling the destroy or removeAllRelations, which object would that be? (would it be the object that created the binding between them in the first place, would it be the sender wishing to destroy the object or maybe the object itself (it can check in it's release, when it's retain count is 1 and destroy the binding there, but i can see problems with this as well)).  Ideally, i would like to find a clean solution that dosen't violate, or circumvent the retain count system, or force some one responsibilty to destroy this binding or manually force deallocation of the object (it should be automatic), since delegation of the responsibilities to do this is a bit blah.  Anyways, i'm curious as to how people have handled this issues, any suggestions are welcome,  thanks for your help.


-wes

----

i've just been looking over the weak reference solution:

http://www.cocoadev.com/index.pl?[[WeakReferences

what do you think of the weak reference solution, is it manageable and scalable?  thanks


-wes

I think I just bumped into the same problem seconds before comming here, iI got it to link fine by loading with @class instead of #include.

