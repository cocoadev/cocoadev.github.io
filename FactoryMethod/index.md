---
layout: page
title: FactoryMethod
---

A class that inherits from NSObject has three roles. The first role is to act as a "factory" for objects the class intends to create, the second role is to define how objects manufactured by the "factory" behave and interface with other objects and the last role of a class is to define services that the "factory" and products of the "factory" implement for other objects. The "factory" is called the ClassObject and an object manufactured by the ClassObject is called an "instance" or InstanceObject.

Creating a factory method:  (discussion follows)

    
+ (MyClass *) objectFromData:(primitive_C_Type)data{

return [self alloc] initUsingDesignatedInitializerWithData:data] autorelease];
}

- ([[MyClass *) initUsingDesignatedInitializerWithData:(primitive_C_Type)data{
self = [super init];      // init is allowed to discard the original objects
myData = data;
return self;
}


The class is a direct subclass of NSObject.

By returning     [ self alloc], subclasses will return instances of themselves from their factory methods with no extra work from you.

The FactoryMethod does all of this in one step for you.  If Object has a FactoryMethod, I might be able to create an instance of Object like this:

Object* myObject = [Object object];

Where "object" is the ClassMethod that will allocate, initialize, and return the object to me.

Many classes in the Cocoa frameworks use the FactoryMethod approach; in general the objects you get from them are given to you with an AutoRelease pending. You should understand the AutoReleasePool before using them.

A FactoryMethod name often starts with the suffix of the class name (i.e. for the NSString class some FactoryMethod names are string, stringWithString:, stringWithCString:). Sometimes factory methods are known as "convenience methods", because they perform three important tasks at once. Factory methods alloc the memory needed to store state information, set up the initial state of the newly-allocated instance and then registers the new instance with the RunTime system to let the memory manager know that a new object needs to be tracked in terms of memory usage. All of this happens by asking the ClassObject (factory) to perform a FactoryMethod: 

A FactoryMethod is simply a convenience for the programmer. Since many operations are implied in a FactoryMethod, I like to think of factory methods as *implicit factory orders*.
Factory methods only save you the time of having to explicitly allocate, initialize and memory-manage instances. *Implicit factory orders*
and *explicit factory orders* are the two fundamental ways you can obtain an instance from a ClassObject (Class Factory).
The customizablility of factory orders is not limited by the choice of using a factory/convenience method (making an*implicit factory order*
or making an *explicit factory order*). 

----

**Lengthy discussion on the use of      self = [ super init ] **

While invoking     self = [ super init ]  is best practice to do in general, it's not *strictly* necessary in this case. NSObject's -init method is documented as doing nothing and always returning     self. However, writing your initializer in a more general fashion means that you don't have to worry about changing anything if you change your superclass later on.

----
The self = [super init] thing is one of those long-standing stylistic issues you can argue about for weeks. It is not good style, and you will never actually need to reassign self when subclassing any of the cocoa classes. I've been using plain [super init]; reassigning self looks to me like an unwholesome stylistic trick.

----
See ClassClusters. Returning something besides     self is standard practice here; it seems to be the only way to get the proper subclass without overriding     alloc (an even bigger evil).

----

Subclasses still get     self returned. See also http://wilshipley.com/blog/2005/07/self-stupid-init.html

----

----
My understanding of your discussion is 

* Using     self = [super init]; is not strictly necessary when creating a direct subclass of a cocoa object or any other object whose API documentation tells me that it is not discarding the original object (or does not tell me it is discarding it).
*The assignment would be required if     init does discard the original object.


----

Shipley said that     self = [super init]; will fail if you return something totally unrelated to the original object.  He neglects to point out that not assigning self will also fail in that situation.  Instead of self pointing at an object that you can't understand, it points at something deallocated (or pending deallocation, depending on retain counts). Not assigning self is *never* correct!  He just likes it better and used that as justification for why it is superior.

----

Shipley pointed out that the only places in real code where     [super init] != self, you've been returned a singleton anyway, and initialising it would be stupid - so you need special code anyway.     self = [super init] won't help you.

----

Adding the extra line is bad because it makes you feel secure while solving nothing. With your argument we should add an infinite amount of code to our programs that looks good and does nothing, because we haven't proved that adding no code will solve all bugs either. --Wil Shipley

----

Whatever you do, at least check if [super init] returned nil. After all, init'ing super might fail, and you wouldn't want to init yourself in that case.

----

There are countless reasons why     [super init] could return a different object but they are all situations that probably would never occur unless you subclass something you aren't supposed to subclass.  The problem with Shipley's claims is that changing self would mean that you are initializing the wrong object.  He ignores the fact that not changing the self pointer when     [super init] returns something other than self also means that you are initializing the wrong object.  I claim that there is at least a class of problems where     self = [super init] makes sense because any sub-class of the current class will be correct to initialize in this way (and, depending on the details of anything else that must be initialized, there could be more situations where this is correct). How would it ever make sense to initialize an object which is not the one returned by your super-class initializer?

Moreover, returning self when     [super init] returned a different object means that the rest of the program is now doing the wrong thing and is probably pointing at a deallocated (or about to be autoreleased) object.  This never makes sense.  One of Shipley's points actually states that it is worth caching the self pointer and only initializing the object when     [super init] returns an identical pointer.  This actually makes sense, but for one reason or another, he doesn't suggest doing it.

----

This argument should present an example where super can sensibly return a new object.

This seems to break inheritance: super cannot allocate an object of the correct size. Even if it does, it gains nothing over having replaced     alloc instead of     init - and, indeed, loses out because of the temporary object created.

Since there's no information passed to     init, there can't be any decision-making that     alloc couldn't have made.

----
The point isn't whether or not it makes sense for init to return something other than self. By assigning self, you only have one return statement since self is nil if     [super init] returned nil. Not assigning self gains you nothing.

----
A pattern that "does absolutely nothing, but do it anyway" makes no sense. Give a case where it actually works better than     if (![super init]) return nil; -- KritTer

----

I have found the **ultimate perfect init method**. Seriously, there are 2 issues here: whether it will ever happen that     self!=[super init] and whether it will do any good to have     self reassigned in that case. It should not happen with current Apple frameworks; what to do in that case is a bit situation-dependent, and assigning self is not necessarily the best and might make things hard to debug. If we fear it may happen one day in some particular class (Apple or others) and want to make sure when that day comes, we know about it, then one solution could be to always do it like this:

    
- (id)init
{
    if  ( self != [super init] )
        [NSException raise:@"IncredibleSituationHere" format:@"Wow, self != [super init] for object <%@:%p>:\n%@",[self class],self,self];
    if ( self !=nil ) {
        //do my initializations...
    }
    return self;
}


Runtime error would likely be caught during development, and then appropriate action can be taken. More code, but a MACRO could help, e.g     SUPER_INIT_CHECK;. And the benefit is that when it happens, you get the front page on Wil Shipley's blog. It is like running SETI@HOME on your computer and you find an alien. --CharlesParnot

*Charles, that bit about the alien is the best line in this whole screed*

----
If     [super init] returns     nil, you don't reassign self, and so self is not nil, you do your initializations, and probably explode in a violent fashion (*much as the Alien would*). You need to introduce a variable to store either the new value returned by     [super init], or the old value of     self for comparison.

----
If     [super init]==nil, then     self!=[super init] and you raise the exception (well, except if     self is also already nil, but then it is fine... but it should probably not happen!). However, if     [super init]==nil, it should probably not raise an exception, but rather set     self to     nil too and return it. There could be another debate about how often     [super init]==nil and what to do with it. --CharlesParnot

----

Good point, I wasn't paying enough attention.     [super init] will return nil much more often than it will return a different non-nil object. Returning nil is a general indication of failure, and this idea is used fairly commonly in Cocoa, whereas destroying self and returning a different object in its place is almost never seen by subclasses in Cocoa.

----

Revised strategy:

    
- (id)init
{
    id newSelf = [super init];
    if ( newSelf == nil )
        self = nil;
    else if ( self != newSelf )     //fixed
        [NSException raise:@"IncredibleSituationHere" format:@"Wow, self != [super init] for object <%@:%p>:\n%@\nCall Wil!",[self class],self,self];
    if ( self !=nil ) {
        //do my initializations...
    }
    return self;
}


----

The above is exactly the same as
 
    
- (id)init
{
    id newSelf = [super init];
    if ( newSelf == nil )
        self = nil;
     if ( self !=nil ) {
        //do my initializations...
    }
    return self;
}

except that no exception is ever raised! How bad do you want the exception ?

The code above without the exception is the same as
    
- (id)init
{
    self = [super init];
    if ( self !=nil ) {
        //do my initializations...
    }
    return self;
}


If the exception isn't important to you, **you just reinvented Apple's reccomended pattern**. Furthermore, it has already been argued that the exception should never happen when subclassing Cocoa classes, so the exception isn't very important.

