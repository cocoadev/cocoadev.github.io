---
layout: page
title: PoseAsAspects
---

**PoseAsAspects** - 

The idea here is that you could call [ACPoser wrapAllMethodsOfClass: [MyClass class]], and get a nice little stack trace of all the methods being called in your class.  And this bit of code does appear to work very well for that purpose, especially if you haven't done anything with MyClass before that point.  Also, you could subclass ACPoser and override the before: and after: methods to perform some specific aspect functionality.  You can get an example of that here: http://www.ccs.neu.edu/home/igotimac/PoseAspect.zip

The implementation uses ClassPosing

But, there is a major problem with this Aspect that need to be worked out, but here's some code of something that does work for some cases.  It seems a major obstacle of this approach lies in that posing as a class only affects new instances of that class, not existing ones.  So, I really just need to figure out where a good place is to instantiate aspects on things that are allocated and used early on. something like: [ACPoser wrapAllMethodsOfClass: [NSObject class]]. but where does it belong? if I put it too early (like in initialize or load methods of ACPoser I get memory leaks or sigbus errors).

Here's ACPoser.h  Note, you'll also need ACMethodIterator
    

#import <Foundation/Foundation.h>


@interface ACPoser : NSObject {

}

+(void)wrapAllMethodsOfClass:(Class)toWrap;

+(void)wrapAllMethodsOfClass:(Class)toWrap beforeAndAfterOf:(Class)toGetMethodsFrom;

+(struct objc_method *)replaceMethod:(struct objc_method *) method;

+(SEL)getReplacementSEL:(struct objc_method *) method;

+(struct objc_method *)replace: (struct objc_method *) method with: (SEL) replacement;

+(struct objc_class *)createClassDefinition: (const char *) name 
    withSuper: (const char *) superclassName;

@end



and ACPoser.m
    

#import "ACPoser.h"
#import "ACMethodIterator.h"
#import </usr/include/objc/objc-class.h>
#import "MyDocument.h"

@implementation ACPoser

//We will want to define another class: MethodStrategy
//which will be passed to this method
//to define which methods to aspect

+(void)wrapAllMethodsOfClass:(Class)toWrap{
    [self wrapAllMethodsOfClass: toWrap beforeAndAfterOf: [ACPoser class]];
}

+(void)wrapAllMethodsOfClass:(Class)toWrap beforeAndAfterOf:(Class)toGetMethodsFrom{
    //add a new class (poser) to the runtime as a subclass of toWrap
    Class poser = [self createClassDefinition: 
        @"replace_" stringByAppendingString: 
        [[[NSString stringWithCString: toWrap->name]] cString] 
        withSuper: toWrap->name];

    //count up the number of methods on toWrap and add that to the totalMethodsToAdd
    int totalMethodsToAdd = 2;
    void *iterator = 0;
    struct objc_method_list *methodList;
    int i;
    methodList = class_nextMethodList(toWrap, &iterator);
    while( methodList != NULL ){
        int count = methodList->method_count;
        totalMethodsToAdd += count;
        methodList = class_nextMethodList(toWrap, &iterator);
    }
    
    struct objc_method_list *methodsToAdd =
        malloc(totalMethodsToAdd * 
        sizeof(struct objc_method) + 
        sizeof(struct objc_method_list));
    methodsToAdd->method_count = totalMethodsToAdd;
    struct objc_method meth;
    meth = *class_getInstanceMethod(toGetMethodsFrom, @selector(before:));
    methodsToAdd->method_list[0] = meth;
    meth = *class_getInstanceMethod(toGetMethodsFrom, @selector(after:));
    methodsToAdd->method_list[1] = meth;

    ACMethodIterator * mit = [[ACMethodIterator alloc] initWithClass: toWrap];

    for( i = 2; i< totalMethodsToAdd; i++){
        struct objc_method *originalMeth = [mit nextMethod];        
        meth = *class_getInstanceMethod(toGetMethodsFrom, [self getReplacementSEL: originalMeth]);
        meth.method_name = originalMeth->method_name;
        methodsToAdd->method_list[i] = meth;
    }
    class_addMethods(poser, methodsToAdd);
    class_poseAs(poser, toWrap);
//    return class_createInstance(toWrap, 0);
}

+(struct objc_method *)replaceMethod:(struct objc_method *) method{
    SEL selector = method->method_name;
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

+(struct objc_class *)createClassDefinition: (const char *) name 
    withSuper: (const char *) superclassName
{
    NSLog(@"creating a subclass of %s", superclassName);
    NSLog(@"named %s", name);
    struct objc_class * meta_class;
    struct objc_class * super_class;
    struct objc_class * new_class;
    struct objc_class * root_class;
    va_list args;
    // Ensure that the superclass exists and that someone
    // hasn't already implemented a class with the same name
    super_class = (struct objc_class *)objc_lookUpClass (superclassName);
    if (super_class == nil)
        return nil;
    if (objc_lookUpClass (name) != nil) 
        return nil;
    // Find the root class
    root_class = super_class;
    while( root_class->super_class != nil )
        root_class = root_class->super_class;
    // Allocate space for the class and its meta class
    new_class = calloc( 2, sizeof(struct objc_class) );
    meta_class = &new_class[1];
    // setup class
    new_class->isa      = meta_class;
    new_class->info     = CLS_CLASS;
    meta_class->info    = CLS_META;
    // Create a copy of the class name.
    // For efficiency, we have the metaclass and the class itself 
    // to share this copy of the name, but this is not a requirement
    // imposed by the runtime.
    new_class->name = malloc (strlen (name) + 1);
    strcpy ((char*)new_class->name, name);
    meta_class->name = new_class->name;
    // Allocate empty method lists
    // We can add methods later.
    new_class->methodLists = calloc( 1, sizeof(struct objc_method_list *) );
    meta_class->methodLists = calloc( 1, sizeof(struct objc_method_list *) );
    // Connect the class definition to the class hierarchy.
    // First, connect the class to the superclass
    // Then connect the metaclass to the metaclass of the superclass
    // Then connect the metaclass of the metaclass to the metaclass of the root class
    new_class->super_class  = super_class;
    meta_class->super_class = super_class->isa;
    meta_class->isa = (void *)root_class->isa;
    // Finally, register the class with the runtime.
    objc_addClass( new_class ); 
    return new_class;
}

/*
* none of the methods below should ever be called as members of this class! (ACPoser)
* and are therefore not declared in ACPoser.h
*/

-(id)replacement{
    [self before: _cmd];
    
    struct objc_method *methodINeedToCallNow = 
        class_getInstanceMethod([self superclass], _cmd);
    id (*test)(id, SEL);
    test = methodINeedToCallNow->method_imp;

    void * toreturn = test(self, _cmd);
    [self after: _cmd];
    return toreturn;
}

-(id)replacement: a{
    [self before: _cmd];

    struct objc_method *methodINeedToCallNow = 
        class_getInstanceMethod([self superclass], _cmd);
    id (*test)(id, SEL, void*);
    test = methodINeedToCallNow->method_imp;

    void * toreturn = test(self, _cmd, a);
    [self after: _cmd];
    return toreturn;
}

-(id)replacement: a r: b{
    [self before: _cmd];

    struct objc_method *methodINeedToCallNow = 
        class_getInstanceMethod([self superclass], _cmd);
    id (*test)(id, SEL, void*, void*);
    test = methodINeedToCallNow->method_imp;

    void * toreturn = test(self, _cmd, a, b);
    [self after: _cmd];
    return toreturn;
}

-(id)replacement: a r: b r: c{
    [self before: _cmd];

    struct objc_method *methodINeedToCallNow = 
        class_getInstanceMethod([self superclass], _cmd);
    id (*test)(id, SEL, void*, void*, void*);
    test = methodINeedToCallNow->method_imp;

    void * toreturn = test(self, _cmd, a, b, c);
    [self after: _cmd];
    return toreturn;
}

-(void)before:(SEL)selector{  //to be overridden in subclasses with actual functionality
    NSLog(@"before %s", selector);
}

-(void)after:(SEL)selector{  //to be overridden in subclasses with actual functionality
    NSLog(@"after %s", selector);
}

@end





----
an Old problem:

Originally the idea of this type of Aspect was use the objc runtime's ability to let subclasses pose as their super class, to dynamically make subclasses of any class we'd like to create an aspect on, add the methods we're going to have as PointCuts, and then swap their IMPs with IMPs to replace methods (taking the right number of arguments) that just call the original method which we have reference to as _cmd on super.. There is a big problem with this approach.  I thought I could just do [super performSelector: _cmd];  But what I really need is a [self performSelectorOnSuper: _cmd]; method.  because clearly it doesn't matter whether I call  [super performSelector: _cmd]; or  [self performSelector: _cmd];.   Because performSelector is implemented in NSObject, not in my Aspect class or any other class involved here. Instead I have to do something like:

    

    struct objc_method *methodINeedToCallNow = class_getInstanceMethod([self superclass], _cmd);
    void (*test)(id, SEL);
    test = (void (*)(id, SEL))methodINeedToCallNow->method_imp;
    test(self, _cmd);



which is not only dependent on the number of arguments I pass, but also on the return type.  Although the objective-c runtime seems to handle all possible return types with only one of two methods objc_msgSend and objc_msgSendV (with super equivalents) so maybe I can use those..?

----

Would an NSInvocation help here? You could even wire up a HigherOrderMessaging trampoline so that     self performOnSuper] someMethod:arg] would work, probably.

----

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/[[NSInvocation.html   NSInvocation is great, but it has no concept of invoking on super.  (just like [super performSelector: _cmd] ends up being exactly the same as doing [self performSelector: _cmd];)   This just boils down to one thing....  

**How do I call some variable selector on my superclass (when I too implement that selector) ?**

[invocation invokeWithTarget:super] doesn't work? Damn. Oh well. Looks like you might need to use the runtime super functions.

So I'm just not worrying about casting my functions to have a particular type, or checking that type.  I just look them up, call them and then hope everything works out.  Can anybody find an instance where the following code does not work?

    
    struct objc_method *methodINeedToCallNow = 
        class_getInstanceMethod([self superclass], _cmd);
    id (*test)(id, SEL, void*, void*, void*);
    test = methodINeedToCallNow->method_imp;

    void * toreturn = test(self, _cmd, a, b, c);
    [self after: _cmd];
    return toreturn;


