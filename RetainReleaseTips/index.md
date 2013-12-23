---
layout: page
title: RetainReleaseTips
---




One of the questions I see come up a lot in newbie questions on Cocoa is how to handle MemoryManagement, especially RetainingAndReleasing.
----
ObjC uses GarbageCollection procedures which require more active involvement on the part of the programmer than do those in Java.
When an object is created, something called its RetainCount is given an initial value of 1. GarbageCollection only kicks in when the RetainCount goes to 0.

If you create an object with     alloc*,     new,     copy* or     mutableCopy* or     retain an existing object, you increment the RetainCount.
But unlike in Java, the **programmer** is then responsible for making certain to free the memory held by the object
by sending either     release or     autorelease (    release is preferred).
The     release and     autorelease messages are the only ways over which you have direct control of decrementing the RetainCount.

*     allocWithZone,     copyWithZone,     mutableCopyWithZone

**RULE:**
If your object creates or retains an object, then your object has the responsibility to release/autorelease that object.

THIS BEARS REPEATING...

If your object creates or retains an object, then your object has the responsibility to release/autorelease that object.

In a single code block, always balance retains and releases.
If you increase the RetainCount for a variable which goes out of scope at the end of a code block be sure to send a     release message to it.

Be sure to override     dealloc and use it to send     release messages to any instance variables that you created with     alloc,     copy, etc..

**BUT NOTE CAREFULLY**
however, that when you add an object to a collection, the collection object increments the member's RetainCount.
When you release a collection object to a RetainCount of 0 and it gets deallocated, all of the member objects have their RetainCount decremented.

**AUTORELEASED OBJECTS**
Cocoa applications maintain an AutoreleasePool (and you can create your own auxiliary versions if you need to)
When you send an     autorelease message to an object it is added to the most recently created AutoreleasePool.
Each pass through the application's RunLoop
(as when the WindowServer forwards an event to your app) begins by creating a new pool and ends by destroying the pool.
In between, autoreleased objects are added to the pool; when an AutoreleasePool is destroyed, all the objects in it get a     release message,
and if that happens to zero out the RetainCount, the GarbageCollection system disposes of the object.

AutoRelease can cause headaches in multithreaded apps - see the WeakReferences topic for strategies

For a good discussion of     autorelease that is perhaps a little roundabout, but does cover all the bases (sorta like Pete Rose, I guess!)
see QuickAutoreleaseQuestion in RetiredDiscussions.

Cocoa methods that return objects can be assumed to be returning     autoreleased objects
so you should not send     release or     autorelease unless you     retain them at some point.

Therefore do ye the same with thine own classes.

**Note:** Class factory methods are the ones which begin with a '+' for example:
    
+ (NSString *)stringWithString:(NSString *)aString;


----

**EXAMPLE:**

    
- (void) setMyString:(NSString*) newString
{
	if (myString != newString) {
		[myString release];
		myString = [newString retain];
	}
}


Notice that for every     retain on an object, we also have a     release. This is also an example of a set accessor method. The     [newString retain] ensures we'll hang on to the object pointed to by     newString while the     [myString release] lets go of the previously     retained value. The next time through this method we'll again     release myString which now contains the most recently acquired value of     newString. See how it ends up balancing out?

(Alternative versions for a set method can be found on the AccessorMethods page.)

----
The same thing works for     alloc] init].

    
- ([[NSString *)getEmptyString
{
	NSString* string = [[NSString alloc] init];

	return [string autorelease];
}


You need to send     autorelease even though you did not explicitly send     retain because     alloc returns a     retained object. You send     autorelease instead of     release because you     return the string. If you send     release to the string before you     return it, the receiver of the     getEmptyString message will get garbage since     releasel causes the object to be deallocated immediately. Using     autorelease accomplishes two goals, it allows the receiver an opportunity to     retain a valid returned object, and it ensures that the object is deallocated at the end of the runloop if no one sends     retain to it.

----
    
- (NSString *) getPiString
{
	NSString* string = [NSString stringWithFormat:@"%f", 3.1415926535];
	return string;
}


In this example we don't need to do anything in terms of     retain or     release since by convention a method of the form

    
+ (NSWhatever*) whateverPlusSomeOtherPotentialArguments...


returns an     autoreleased object. This     stringWithFormat: message handles the     retain (    alloc] init]) and     release (    autorelease as in our previous example) for us so it's just up to us to     return the object to the caller.
In other words, if you return an autoreleased object from one of your methods. let the user of that object worry about retaining it.

----

You should use the same sort of     retain/    release techniques in all of your own code. By implementing object setters like above for your instance variables, you'll end up with fewer memory leaks or stale pointers because you'll force the     retain/    release rule handling into short, easy to understand methods.

----

If you'd like some more info about this subject, our [[MemoryManagement page is a good place to start.

----

**WARNING:** ABOUT AUTORELEASED OBJECTS CREATED IN LOOPS

Watch out for autoreleased objects created in a loop.  If you find yourself doing something like this:

    
for (...50 times...) {
  blah = [NSString stringWithString: ...];
  // do stuff with blah
}


you should use manual allocation instead, since the lifetime of 'blah' is very clear.

    
for (...50 times...) {
  blah = [[NSString alloc] initWithString: ...];
  // do stuff with blah
  [blah release];
}


This will avoid (essentially) adding 50 items to a     NSMutableArray and
then going through them and sending     [object release] to each one when
the AutoreleasePool is freed.  Not to mention the extra memory used
by those 50 strings.

*Note also that if you find yourself allocating and releasing 50 objects in quick succession in a loop such as this, it may be worth finding ways of reusing a single object instead - it all depends on what you are doing with the objects. If the strings above were being retained by some other section of code for use later, retain-release would be the appropriate construction. If they were not, you could consider using a single mutable string instead, saving the needless creation/deletion.*

----

**COMMENTARY**

The only methods that I know of that can explicitly create a new instance of an object are the alloc and copy types (add more if you know of more). The first thing to get straight in your head about memory is to think of objects this way. If your object didn't explicitly alloc it or explicitly copy an object then your object didn't create a new object, therefore you don't need to worry about releasing anything.

Now the tricky part is not to get confused with thinking that your object created an object by calling a factory method, because your object isn't creating the object. The Class object performing the factory method is creating the object in this case because this Class object is explicitly allocating or explicitly copying the instance of the object being returned to your object. It is safe to assume that all factory methods are playing by the rules, so all factory methods are releasing/autoreleasing every thing they're creating. 

----

NSObject defines a     +new method as a shortcut for alloc / init. It's not used often, and probably shouldn't be, but it's there.

    NSArray *myArray = [NSArray new]

is equivalent to

    NSArray *myArray = [[NSArray alloc] init]

[http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSObject.html]

----

**I believe that most methods which return objects will return obj retain] autorelease] (so that the object will hang around even after the possessive object is gone), but otherwise right.**

*Actually, that is a very suspect assumption. Foundation collections, for instance, will most probably not bother, because returning an object is a lot cheaper than retaining and autoreleasing it first. Thus, retaining in your own code is vital. -- [[KritTer*

**Fair enough, it's suspect; still and all, you see it done a lot in sample code. -- RobRix**

----

Auto-released objects are taken care of at the end of the request-response cycle. If, for example, you're in a method that was called by pressing a button in the GUI, the auto-released variables will stick around until the execution of that method is finished and control goes back to the user. (If you're not using MultiThreading, the GUI will be unresponsve while   the method is running.)
That's also the reason why crashes that have anything to do with not retaining an auto-released object usually occur right before the method in question is finished.

Please correct if there's anything wrong in there.

-- JensBaumeister

----
The answer to the question 'When will an autoreleased object be released?' is: When the autorelease pool itself gets released. There can be more pools than the one in the main runloop. See AutoreleasePool.

----

You should make a point to talk about what method to create instances. The "duh" response would be to say that you should always create instances in an init method (i.e. init, initWithFrame.........), but sometimes you might be tempted to create objects after a window has loaded from a nib file. I got burned when I created a couple of objects in the viewDidMoveToWindow so I could get some values from the window at runtime thinking that this was safe since the view in question was only going to be placed in the window once. Boy was I wrong!!!! there might be cases when a view will receive a "viewDidMoveToWindow" message more than once even if it is only added once to its superview. Since my code looked fine on paper (each retain/alloc/copy had the required release/autorelease) I couldn't figure out where the memory leak was. It took me about an hour to trace the leak to an NSDictionary initialized twice because the viewDidMoveToWindow method was called twice (but the view was properly released and deallocated once). 

It's easy to think that if you only have one instance variable you only have one object to keep track of. Don't fall into this trap. One instance variable has the ability to point to any number of objects over time, just it can't point to more than one object at the same time. In the case above I had one NSDictionary pointer that appeared on paper to point to only one NSDictionary instance for the life of the object. 

The moral of the story is to initialize all instances in an init method (awakeFromNib has never given me problems, but it will now be a place I check if I can't find a leak anywhere else). And don't forget to release/autorelease everything you create!!!

----

what I did to solve my problem:

    

-(id)init {
    self = [super init];
    myView = nil;
    return self;
} 

-(void)viewDidMoveToWindow {
    NSRect frame = [self window] delegate] frameForNewView];
    if (!myView) {
         myView = [[[[NSView alloc] initWithFrame:frame];
         [myView autorelease];
         [self window] contentView] addSubview:myView];
    }
}

-(void)dealloc {
    [myView release];
    [super dealloc];
}



Those new to Cocoa:

The code above is not that advanced, but if you are scratching your head about it I'll explain. All of the methods above belong to a subclass of an [[NSView that supports (is a custom class for) an instance of a custom view frozen in a nib file.  Sometimes you might want to get information from the window at runtime, but the problem is an NSView's instance variable for "window" is not set until the view in question becomes a subview of the window. Using viewDidMoveToWindow allows you to create and modify objects based on information obtained from the window the view is placed in.

I just wanted to bring attention to this type of problem because I have seen many code examples use awakeFromNib as an initializer method without setting instance variables to nil and using NSAssert as a precaution. Like I mentioned above, it probably is safe to use awakeFromNib as an initializer method, but thinking awakeFromNib is ok might lead to poor assumptions (like me assuming viewDidMoveToWindow was a failsafe substitute for an initializer method). 

*Instance variables in ObjC objects are guaranteed to be initialized to nil when an object is created, so it is not necessary to do so manually.*

----

There's an extra release in the above code. Look at myView. It is alloc-ed, then autoreleased, the released again in dealloc. If dealloc were to be called, then release would be sent to a deallocated object and probably crash the app. Get rid of the autorelease inside viewDidMoveToWindow (since you're balancing the alloc with the release inside your dealloc method).

In addition to being called when a view is added to a window, viewDidMoveToWindow is also called when the view is removed from a view hierarchy. That might be why you're seeing it called multiple times, but you only add the view to a window once. Treat it like a notification that "the window has changed" and look at [self window] to see what the new window is (it could be nil).

This doesn't really seem like a discussion about retaining/release, so perhaps this section should be refactored elsewhere?

----

To any volunteers who follow up on this re-factoring:
I am perplexed by the final discussion above, which begins with the words "You should make a point to talk about what method to create instances."
This seems to have rather less to do with MemoryManagement per se and more to do with procedures for objects to initialize their variables.
Yet here it is in RetainReleaseTips. I suspect that it is valuable and should be transplanted to a more appropriate place, dealing with initialization.
Not everything that causes MemoryLeaks can be dealt with directly as a MemoryManagement problem.

----

Replace function 

    
This can be used for retained reference updates.eg replace(&field,newValue);
This might be better as a macro, but personally I don't like macros.

void inline replace(id *old,id new)
{
    [new retain];
    [*old release];
    *old = new;
}

This can be used in dealloc for each field, 
remember to balance any stray alloc|copy|new|mutableCopy 
... and sleep easily at night. -- MikeAmy

void inline clear(id *old)
{
    [*old release];
    *old = nil; // of course you could change this to a zombie style object for debugging
}

MPWFoundation has some other macros.
Note you don't want to retain things that might lead to a cycle

----
Assume that i have some instance variable which i need till the end of my application..then when is the suitable time to release  this instance variable? do we actully need to release this? because as soon as you quit the application it will be released. and i have seen many times dealloc will not be called.So it is waste releasing there

