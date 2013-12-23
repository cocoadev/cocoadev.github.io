---
layout: page
title: ClassCategories
---

**Categories**


Categories add methods to existing classes.
In contrast, subclassing creates new classes.

**Uses:**

- If you use a category to add a method to a framework class, instances  of that class returned by a framework will have the method.  If you add  a method via a subclass, you may have no way to convince the framework  to return instances of your subclass instead of whatever class the  framework was compiled with.

- Cocoa includes several categories on NSObject which effectively add  methods to any object you encounter.  This practice is sometimes called  an informal protocol.  InformalProtocols

- Categories are sometimes used just to break the implementation of a  class into multiple files.

- Categories are a handy way to extend class clusters which are tricky to  subclass. ClassClusters

- Categories may be used to preserve the Model/View/Controller partitioning or other desirable partitioning.  Parts of a single class can be implement in different partitions/subsystems as appropriate.

For example, NSString is defined in the Foundation framework where  there is no knowledge of display issues.

The Application Kit framework contains the NSStringAdditions category that adds display capabilities to every NSString object if and only if  
the Application Kit framework is linked into your application.  http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSStringAdditions.html

There is a clear partitioning of functionality and it is possible to  use strings without requiring linkage to the Application Kit, but if  you do link to the Application Kit, you get the extra features  automatically. Categories provide a means of extending a class without having to subclass it. This is very useful if you want a large group of classes to inherit a behavior, including ready-made ones. For instance, if you made a category of NSObject, all classes that inherit from NSObject would then inherit the additions made in your category.

- You can also use categories to declare "private" object methods. In this case, "private" means API you don't want to publish in your public header file, but you want the compiler to recognize all the same. External clients can always call whatever methods they like, so this isn't private in a C++ sense, but it's still nice to separate expected public API from the creepy internals of your object. See KissyFaceView for an example.

----
Simulate adding instance variables to an existing class via categories.

Technically, categories can't add instance variables to a class. But this can be simulated. The trick is to use a static NSMutableDictionary that maps instances of the base class to dictionaries of new 'instance' variables.

To avoid memory leaks we need to swizzle in a dealloc method.  Go read up on MethodSwizzling if you haven't yet.


    

static NSMutableDictionary *instanceIDToIvars = nil;
static BOOL needToSwizzleDealloc = YES;

@implementation BaseClass (PRE__MyCategory)

- (id)PRE__instanceID
{
    return [NSValue valueWithPointer:self];
}

- (NSMutableDictionary *)PRE__ivars
{
    NSMutableDictionary *ivars;
    
    if (needToSwizzleDealloc)
    {
    	MethodSwizzle([BaseClass class], 
    	              @selector(dealloc), 
    	              @selector(PRE__deallocSwizzler));
    	needToSwizzleDealloc = NO;
    }

    if (instanceIDToIvars == nil)
    {
        instanceIDToIvars = [[NSMutableDictionary alloc] init];
    }
    
    ivars = [instanceIDToIvars objectForKey:[self PRE__instanceID]];
    if (ivars == nil)
    {
        ivars = [NSMutableDictionary dictionary];
        [instanceIDToIvars setObject:ivars forKey:[self PRE__instanceID]];
    }
    
    return ivars;
}

- (void)PRE__deallocSwizzler
{
    [instanceIDToIvars removeObjectForKey:[self PRE__instanceID]];
    if ([instanceIDToIvars count] == 0)
    {
        [instanceIDToIvars release];
        instanceIDToIvars = nil;
    }
    
    [self PRE__deallocSwizzler];
}

-(id)myIvarOne
{
    return self PRE__ivars] objectForKey:@"myIvarOne"];
}

-(void)setMyIvarOne:(id)aValue
{
    [[self PRE__ivars] setObject:aValue forKey:@"myIvarOne"];
}

-(id)myIvarTwo
{
    return [[self PRE__ivars] objectForKey:@"myIvarTwo"];
}

-(void)setMyIvarTwo:(id)aValue
{
    [[self PRE__ivars] setObject:aValue forKey:@"myIvarTwo"];
}

@end



If you so desire, you can also use categories to make [[InformalProtocols; but please, please read up on ProtocolsTooCasualSmell first!

We have some CategorySampleCode available, too.

----

Code above is updated (old code didn't work well).  'could probably use a few pairs of eyes to make sure I didn't do something dumb.

----

See http://www.cocoabuilder.com/archive/message/cocoa/2004/12/4/123013 for Erik Buck's version of things, which is largely the same.  One notable difference is the use of NSMapTable instead of NSMutableDictionary.  Since I haven't seen NSMapTable before, I'm still working out why he prefers it to NSMutableDictionary. 

*
RTFA ;)

"NSMapTable is used in the following example because it provides a little more flexibility
than NSDictionary. Dictionaries always copy their keys, but in the following
example it is necessary to store keys without copying or retaining them."
*

----
**Rejected alternative**


As an alternative approach, you could swizzle out the *allocator* for one that allocates a bit more memory, then use that. This saves on memory, but cannot be done twice by different categories, unlike the above. The trick would be to create a subclass with the variables you need, then call its allocator (since you probably don't know how much space is "hidden" by the system).

*Would this trip up subclasses of the base class or not?  I don't understand how the alloc method is implemented, so I can't quite say, but I don't think it's reasonable to assume there are no subclasses of the base class.  Also, there's some issue of where to do the alloc swizzle;  you shouldn't use a +initialize because it would override the base class +initialize.*

The alloc method is implemented more or less like this (this is a demonstration, not actual fact):
    
+ alloc {
   id obj = malloc(self->instance_size);
   obj->isa = self;
   return obj;
}

Making alloc allocate more memory than necessary can be done, but it will still be very difficult to make such a system work correctly with subclasses.

As far as +initialize goes, I believe the runtime does The Right Thing regarding category implementations of it, and invokes every implementation of +initialize exactly once, but I can't find anything to back that up. The +load message does do this (it's explicitly stated in TFM) so you could always put it there.

*
I'm not sure I can find it again, but I explicitly saw that +initialize does *not* do the right thing (an Apple engineer was complaining that he thought it was broken behavior, but was too late to change).  The situation is that +initialize is an inherited method like any other, and can be overridden by subclasses and categories.  It will be called once before the use of a method from a class.  This means that if you use a +initialize in BaseClass, and don't override it in SubClass, then the method will be called once to initialize SubClass and once to initialize BaseClass.  If +initialize is overridden by a category, then the new +initialize will replace the old one.  It will *never* be called if the class has already been initialized. 

That's good about load though, I didn't remember what was the case there.

Update: found it.  http://lists.apple.com/archives/objc-language/2004/Mar/msg00004.html
*

It's probably best to use poseAs:, then?

*
Best to use it for what?  The code at the top works fine for adding fake instance variables.  
*

Sorry, I meant "best if you want to save memory costs, which was the stated goal of this alternative."

*
One further problem with using +load : The order that classes/categories load is undefined.  It's specifically mentioned that a class may load before its superclass.  That said, I don't know if it's safe to swizzle something in the base class from a category's load, since it may be possible that the base class doesn't exist yet.
*

----
----

If your needs are minimal, you can avoid the dealloc-swizzle by using a static C type and encode your instance's pointer and the information you want to save together by ANDing it or something. I think that'd work...

----

Want to elaborate?  I can't tell what you mean..  but it sounds like you won't be able to handle an arbitrary number of objects, since your static variable only has so much space.

----

Yeah, I guess there is that. It was just a thought I came up with to avoid the dealloc swizzle. What I meant was, if you needed to save some state across invocations, you could AND a boolean with your instance's self value, then decode it later. I guess you could still use it for a global/singleton type situation (but then, of course, there'd be no need to AND anything.)

Hmm... couldn't you put them in a C array instead?
----

That doesn't solve the problem of not leaking memory, and an array is not as useful as a map-type structure like an NSDictionary for this purpose.  Why don't you write out an implementation to straighten out some ideas. 

ANDing data to encode it is probably not helping you, btw.  Why not just use more than one variable? 

*okay, i was talking nonsense. :} NSDictionary is the way to go.*

