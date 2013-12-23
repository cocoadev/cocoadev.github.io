---
layout: page
title: ACAspect
---



In AspectCocoa, an aspect is created as follows:
    
 ACAspect * myAspect = [[ACAspect alloc] initWithPointCut: somepointcutobject 
                                          andAdviceObject: someadviceobject];

Once an aspect has been created, it must be loaded in order for it to be applied:
    
 [myAspect load];

To remove it's functionality, it must be unloaded:
    
 [myAspect unload];


----

    
 /* 
     Initialize this ACAspect object with a pointcut and an advice object
 */
 -(id)initWithPointCut: (id)pc andAdviceObject: (id)adv;
 
 /* 
     Load the aspect, apply it's advice to it's point cut
 */
 -(void)load;
 
 /* 
     Unload the aspect, unapply it's advice from it's point cut
 */
 -(void)unload;
 
 /* 
     returns whether or not this aspect is currently loaded
 */
 -(BOOL)isLoaded;

