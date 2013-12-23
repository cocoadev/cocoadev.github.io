---
layout: page
title: AddClassAspects
---

**AddClassAspects**  - 

So PoseAsAspects won't work on NSObject and need to be loaded before Object get instantiated.  AddMethodAspects aren't working because class_addMethods isn't working.  So let's create a new class for the class we're loading an aspect on.  And instead of posing this new class as the other one, we set up the IMP pointers of the original class to point to replacement methods we define.  These replacement methods then lookup the original IMP pointers by looking to the replacement class that we've created.  here goes...

http://www.ccs.neu.edu/home/igotimac/AddClassAspect.zip

    

#import "ACAddClassAspect.h"
#import "ACMethodIterator.h"
#import </usr/include/objc/objc-class.h>

@implementation ACAddClassAspect

+(void)wrapAllMethodsOfClass:(Class)toWrap{
    [self wrapAllMethodsOfClass: toWrap beforeAndAfterOf: [ACAddClassAspect class]];
}

+(void)wrapAllMethodsOfClass:(Class)toWrap beforeAndAfterOf:(Class)toGetMethodsFrom{
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
        //add identical method from the toWrap class
        struct objc_method *originalMeth = [mit nextMethod];        
        methodsToAdd->method_list[i].method_name = originalMeth->method_name;
        methodsToAdd->method_list[i].method_types = originalMeth->method_types;
        methodsToAdd->method_list[i].method_imp = originalMeth->method_imp;
        //replace methods in the toWrap class 
        meth = *class_getInstanceMethod(toGetMethodsFrom, [self getReplacementSEL: originalMeth]);
        originalMeth->method_imp = meth.method_imp;
    }
    class_addMethods(poser, methodsToAdd);
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

+(struct objc_class *)createClassDefinition: (const char *) name withSuper: (const char *) superclassName{
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
    struct objc_method *methodINeedToCallNow;
    Class withOriginalMethods = objc_lookUpClass( 
    @"replace_" stringByAppendingString: [[[NSString stringWithCString: self->isa->name]] cString]);
    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, @selector(before:));
    id (*before)(id, SEL, void*);
    before = methodINeedToCallNow->method_imp;
    before(self, @selector(before:), _cmd);

    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, _cmd);
    id (*around)(id, SEL);
    around = methodINeedToCallNow->method_imp;
    void * toreturn = around(self, _cmd);

    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, @selector(after:));
    id (*after)(id, SEL, void*);
    after = methodINeedToCallNow->method_imp;
    after(self, @selector(after:), _cmd);

    return toreturn;
}

-(id)replacement: a{
    struct objc_method *methodINeedToCallNow;
    Class withOriginalMethods = objc_lookUpClass( 
    @"replace_" stringByAppendingString: [[[NSString stringWithCString: self->isa->name]] cString]);
    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, @selector(before:));
    id (*before)(id, SEL, void*);
    before = methodINeedToCallNow->method_imp;
    before(self, @selector(before:), _cmd);

    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, _cmd);
    id (*around)(id, SEL, void*);
    around = methodINeedToCallNow->method_imp;
    void * toreturn = around(self, _cmd, a);

    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, @selector(after:));
    id (*after)(id, SEL, void*);
    after = methodINeedToCallNow->method_imp;
    after(self, @selector(after:), _cmd);

    return toreturn;
}

-(id)replacement: a r: b{
    struct objc_method *methodINeedToCallNow;
    Class withOriginalMethods = objc_lookUpClass( 
    @"replace_" stringByAppendingString: [[[NSString stringWithCString: self->isa->name]] cString]);
    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, @selector(before:));
    id (*before)(id, SEL, void*);
    before = methodINeedToCallNow->method_imp;
    before(self, @selector(before:), _cmd);

    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, _cmd);
    id (*around)(id, SEL, void*, void*);
    around = methodINeedToCallNow->method_imp;
    void * toreturn = around(self, _cmd, a, b);

    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, @selector(after:));
    id (*after)(id, SEL, void*);
    after = methodINeedToCallNow->method_imp;
    after(self, @selector(after:), _cmd);

    return toreturn;
}

-(id)replacement: a r: b r: c{
    struct objc_method *methodINeedToCallNow;
    Class withOriginalMethods = objc_lookUpClass( 
    @"replace_" stringByAppendingString: [[[NSString stringWithCString: self->isa->name]] cString]);
    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, @selector(before:));
    id (*before)(id, SEL, void*);
    before = methodINeedToCallNow->method_imp;
    before(self, @selector(before:), _cmd);

    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, _cmd);
    id (*around)(id, SEL, void*, void*, void*);
    around = methodINeedToCallNow->method_imp;
    void * toreturn = around(self, _cmd, a, b, c);

    methodINeedToCallNow = class_getInstanceMethod(withOriginalMethods, @selector(after:));
    id (*after)(id, SEL, void*);
    after = methodINeedToCallNow->method_imp;
    after(self, @selector(after:), _cmd);

    return toreturn;

}

-(void)before:(SEL)selector{  //to be overriden in subclasses with actual functionality
    NSLog(@"before %s", selector);
}

-(void)after:(SEL)selector{  //to be overriden in subclasses with actual functionality
    NSLog(@"after %s", selector);
}


@end



