---
layout: page
title: LookupAspects
---

**LookupAspects** - 

see ACProblemOfVariableTypes

The solution to the problem of where to store the original IMP pointers of the functions is the major difference between all of our implmentation possibilities.  This one uses the good old fashioned NSMutableDictionary via some other classes: ACAdviceList and ACAspectManager (which has an ACAdviceLookup ).  See discussion following the code for alternate lookup possibilites/suggestions. http://www.ccs.neu.edu/home/igotimac/LookupAspect.zip defines all these classes and a little example...

And this leads me to AspectCocoaBugs

    

#import "ACLookAspect.h"
#import "ACAdviceList.h"
#import "ACMethodIterator.h"
#import </usr/include/objc/objc-class.h>
#import "ACAspectManager.h"

/*
struct objc_method {
	SEL method_name;
	char *method_types;
	IMP method_imp;
};

struct objc_method_list {
	struct objc_method_list *obsolete;
	int method_count;
	struct objc_method method_list[1];	// variable length structure
};
*/

@implementation ACLookAspect
+(id)wrapAllOfClass: (Class)toWrap withAdviceObject: (id)advice{
    ACMethodIterator * iterator = [[ACMethodIterator alloc] initWithClass: toWrap];
    //go through every method on the class and give it a replacement IMP
    ACAspectManager * manager = [ACAspectManager sharedManager];
    while([iterator thereIsAnotherMethod]){
        struct objc_method * method = [iterator nextMethod];
        //ask the pointcut object if we should wrap this method
        //(not yet implemented)
        [manager willWrapMethod: method onClass: toWrap withAdviceObject: advice];
        [self replaceMethod: method];
    }
}

+(struct objc_method *)replaceMethod:(struct objc_method *) method{
    SEL selector = method->method_name;
    NSLog(@"selector %s", selector);
    return [self replace: method with: [self getReplacementSEL: method]];
}

+(SEL)getReplacementSEL:(struct objc_method *) method{
    int num = method_getNumberOfArguments( method );
    if( num == 2) //0 args + 2 hidden args
        return @selector(replacement);
    if( num == 3)
        return @selector(replacement:);
    if( num == 4)
        return @selector(replacement:r:);
    if( num == 5)
        return @selector(replacement:r:r:);
}

+(struct objc_method *)replace: (struct objc_method *) method with: (SEL) replacement{
    struct objc_method *meth = class_getInstanceMethod([self class], replacement);    
    method->method_imp = meth->method_imp;
    return method;
}

/*
* none of the methods below should ever be called as members of this class! (ACLookAspect)
* and are therefore not declared in ACLookAspect.h
*/

//no return type casting, because we don't know what the return type will be...
-replacement{
    //get the ACAdviceList
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil)
        return;
    int i;
    void * toReturn;

    //advice invoke all the Befores
    [advice invokeBefores:_cmd onObject: self arg1: nil arg2: nil arg3: nil];
    
    //If we decide to support around
    //it would have to happen here
    IMP toInvoke = [advice getIMP];
    toReturn = toInvoke(self, _cmd);

    //advice invoke all the Afters
    [advice invokeAfters:_cmd onObject: self returned: toReturn arg1: nil arg2: nil arg3: nil];
    
    return toReturn;
}

-replacement: a{
    //get the ACAdviceList
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil)
        return;
    int i;
    void * toReturn;

    //advice invoke all the Befores
    [advice invokeBefores:_cmd onObject: self arg1: a arg2: nil arg3: nil];
    
    //If we decide to support around
    //it would have to happen here
    IMP toInvoke = [advice getIMP];
    toReturn = toInvoke(self, _cmd, a);

    //advice invoke all the Afters
    [advice invokeAfters:_cmd onObject: self returned: toReturn arg1: a arg2: nil arg3: nil];
    
    return toReturn;
}

-replacement: a r: b{
    //get the ACAdviceList
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil)
        return;
    int i;
    void * toReturn;

    //advice invoke all the Befores
    [advice invokeBefores:_cmd onObject: self arg1: a arg2: b arg3: nil];
    
    //If we decide to support around
    //it would have to happen here
    IMP toInvoke = [advice getIMP];
    toReturn = toInvoke(self, _cmd, a, b);

    //advice invoke all the Afters
    [advice invokeAfters:_cmd onObject: self returned: toReturn arg1: a arg2: b arg3: nil];
    
    return toReturn;

}

-replacement: a r: b r: c{
    //get the ACAdviceList
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil)
        return;
    int i;
    void * toReturn;

    //advice invoke all the Befores
    [advice invokeBefores:_cmd onObject: self arg1: a arg2: b arg3: c];
    
    //If we decide to support around
    //it would have to happen here
    IMP toInvoke = [advice getIMP];
    toReturn = toInvoke(self, _cmd, a, b, c);

    //advice invoke all the Afters
    [advice invokeAfters:_cmd onObject: self returned: toReturn arg1: a arg2: b arg3: c];
    
    return toReturn;
}

@end




So we could pass NSObject to the code below and it would work.. but what do we do with all of NSObjects original methods, so that we can access them when we need them (sorted by selector somewhere)
Does anybody know of a good data structure for make a dictionary with Selectors as keys and Methods as entries?
how would I go about creating such a data structure?


----

I believe selectors and objc_methods don't need retaining - the pointers never go away. (selectors are constant c-strings in your global space, objc_methods are structs which probably point into per-class memory allocated by the runtime.)

Given that, you could use a CFDictionary or CFMutableDictionary, specifying NULL for the callback structs when you create it. That will give you a nice simple key-value relationship which can be used to store any pointer-sized value without requiring it to be an NS/CF object. Once you create the dictionary with CF, it will be toll-free bridged to NSDictionary and you should be able to add and retrieve items via the NSDictionary APIs like normal. Even if you find that you can't, it's not much trouble to use the CFDictionary APIs if you have to. That should get you something functional.

----

One additional thing to note is that I've heard some talk that CF's default hash method is suboptimal for storing pointers, because it only uses the low bits for the hash value... since pointers (and function pointers, if you go that route) tend to be 16-byte aligned, many of those bits are zero, which can cause more hash collisions than necessary. So a possible optimization would be rather than using NULL callbacks in the CFDictionary, to pass in an empty struct that only has a hash callback. A faster hash would be something like

    
CFHashCode BetterPointerHash(const void *value)
{
 CFHashCode hash = (CFHashCode)value;
 return (hash >> 4) ^ (hash << (sizeof(const void*)*8 - 4);
}


i.e., rotating the incoming pointer four bits to the right. As always, you should test this to make sure it really makes things faster and reduces the number of hash collisions.

(the hash problem is also discussed here: http://www.mulle-kybernetik.com/artikel/Optimization/opti-7.html )

