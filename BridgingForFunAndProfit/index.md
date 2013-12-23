---
layout: page
title: BridgingForFunAndProfit
---



See also : TollFreeBridging, classes that are TollFreeBridged

What is Toll Free Bridging?

Toll free bridging is the mechanism that allows us to transparently use CoreFoundation types as Foundation classes, and vice versa.  Classes such as NSString, NSDictionary, NSArray are bridged to types such as CFString, CFDictionary, CFArray, etc.

This is one of the few bonuses of having Foundation built on top of CoreFoundation, rather than being a 'pure' Objective-C library. But then, if Apple hadn't spent so much time developing Carbon, we might have everything in pure ObjC, like we had with NeXTSTEP, and not have to bugger about with CoreFoundation at all... But I digress.

How do we use Toll Free Bridging?

Look at the following fragment of code.

    
NSString * ocString = [NSString stringWithString:@"pants"];
CFStringRef cfString = CFStringCreateWithCString(NULL,
                                                "pants",
                                                kCFStringEncodingASCII);

BOOL eqTest1 = [(NSString *)cfString isEqual:ocString];
Boolean eqTest2 = (CFStringCompare((CFStringRef)ocString,
                                   cfString, NULL)
                    == kCFCompareEqualTo);


Here we create two strings using the two different methods, and them compare them in two different ways.  Both comparisons will return true, as the strings are equal, despite being created from completely different environments.  The astute reader will also notice which environment is easier to create and compare stuff in ;-)  

Whatever.  We can happily cast these types backwards and forwards.  And for most of us, that's enough.  We look in the docs, see 'toll free bridged', and happily go on our merry way.

But.  Not all of CoreFoundation is bridged.  Not even nearly.  Which means that, for certain operations, not all of which are obscure, we have to go to CoreFoundation to get the job done.  And that takes time.  Lots of it.  The documentation is obtuse, and you need to do lots of crossreferencing.  Maybe I'm being harsh here, but that's been my experience.

So, why isn't all of CF bridged?  Is Apple just lazy?  Or is there some other reason?

To answer those questions we need to understand how bridging actually works.  And that means looking into Apple's codebase.  Now, Foundation itself is closed source, but large parts of CoreFoundation have been opensourced as part of the Darwin Project.  Yep, it's all sitting there in Apple's CVS repository, waiting for you to go and look at it.  On top of that, the Objective-C guts are also there.

So, how does it all work?

Well, as we can cast happily from one type to another, and both our types are pointers, we should already be aware that the contents of both types of object can be represented interchangeably by a single block of memory.  Therefore, the definitions of those blocks must be interchangeable and identical between the two different systems.

So, let's take a closer look at what the guts of an Objective-C object looks like.

First off, we know that its type is a pointer, and that pointer can always be converted to type id, which, as it happens, is merely a pointer to a structure of type objc_object - see /usr/include/objc/objc.h.

Structure objc_object is defined as follows:

    
struct objc_object
{
	struct objc_class *isa;
	/* ...variable length data containing instance variable values...
*/
};


All instances of Objective-C classes are, are pointers to blocks of memory, the first part of which is a structure of type objc_object.  For full chapter and verse on how this works, head off to your local documentation and read the 'Objective-C Programming Language' documentation, particularly the gnarly stuff you ignored the first time through (and, quite likely, the following ones as well).  What it boils down to, though, is that the memory block representing an instance has a pointer to its class, followed by the instance variables.

This, of course, is much like a structure, although there is no formal structure definition for a given class as such, although one can almost be obtained by using @defs(classname.  Not that we have to do that very often, thank the lord.

Now let's dig into the guts of CoreFoundation

CF types are opaque structures - none of the normally available headers give away the structure of their internals, so we need to download the CF source from Darwin.  And what do we find?

    
/* All CF "instances" start with this structure.  Never refer to
 * these fields directly -- they are for CF's use and may be added
 * to or removed or change format without warning.  Binary
 * compatibility for uses of this struct is not guaranteed from
 * release to release.
 */
typedef struct __CFRuntimeBase {
    void *_isa;
#if defined(__ppc__)
    uint16_t _rc;
    uint16_t _info;
#elif defined(__i386__)
    uint16_t _info;
    uint16_t _rc;
#else
#error unknown architecture
#endif
} CFRuntimeBase;


Well, well, well.  A CF structure also starts with a pointer to its class.  Or a pointer to something, at least.  That is followed by two 16 bit numbers, and then the rest of the object definition.  Here we actually do have a structure, although Apple doesn't like giving it away - we're not supposed to play with it.

We can now begin to see how bridging works at a high level.  As long as the isa pointer is set to the point at the Objective-C class, and the Objective-C class definition has the same type of variables in the same place, and both definitions are exactly the same size, we can happily typecast between the two, and therefore use them in both runtimes.

We do, however, need to be extremely careful.  The structures absolutely must be the same size - if, for example, we created an Objective-C class that was bigger than the CF type, the additional data might well be thrown away by any CF functions, and if we try to access those extra variables in a CF-created object cast to ObjC, we are going to overwrite random memory.

And, most importantly of all, that isa pointer.  It needs to point to the right Objective-C Class object, or the ObjC runtime is going to start coughing in a very nasty way.

So, any bridged class, when instantiated through CF, must be having its isa pointer initialised the right way.  How is CF doing that?

Cunningly enough, CoreFoundation has a table that maps CF types to ObjC classes.  It really is that simple.  During CF intialisation, the various bridged types are initialised in the table, and the rest point to a 'dummy' class, NSCFType.  When an object is created via CF, it looks up the type it's creating, uses that as an index into the table, and sets the isa pointer to whatever class object happens to be floating around at that index in the lookup table.  Simple really.  Look in Base.subproj/CFRuntime.c function _CFRuntimeCreateInstance() and the definition for __CFISAForTypeID() in Base.subproj/CFInternal.h (the one in the #ifdef __MACH__ block)

I couldn't find anything in CF that seemed to be setting the values in this table explicitly except for the default values, and some stuff that un-defaults for known bridged classes - I suspect the actual values get stuffed in there as part of the ObjC class initialisation (proprietary Apple code), and that is good news for us, as it opens up the possibility of bridging our own classes

Then, to allow for the reverse casting to work, there is an undocumented method on NSObject

    
-(CFTypeID) _cfTypeID;


By default, this returns _kCFRuntimeNotATypeID, and bridged classes override to return the typeid they are bridged to.

All pretty simple, really, despite the horrendous code in CF to make it all work.  It really is quite ugly.

Back to the original question.  Why isn't all of CF bridged?  

Sadly, it looks as though the reason is - Apple is either lazy, or for some reason doesn't consider Cocoa needs ObjC-accessible trees, XML parsers, and the like.   There appears to be no reason all of CF couldn't be bridged to equivalent classes.

So, what can we do?  Can we write our own bridged classes, now we have a handle on how it works?

Well, (and Apple probably aren't going to thank me for this), yes.  Yes, we can.  Quite easily, as it happens.

All we need to do is this:

*Make sure our class has the required structure to bridge to the CF type in question
*Make sure our class identifies itself as the right CFType
*Patch CF's class lookup table


Let's have a little example.  A useful thing to bridge might well be CFTree.  So, we get up the CF collections documentation, wait about a week for it to all load, work out what we want our class to look like.  We can definitely make it easier to use, that's for sure.

Our public interface might look a bit like this:

    
@interface xxTree (Public)
+ (id) treeWithObject;

- (id) object;

- (xxTree*) parent;
- (xxTree*) root;

- (BOOL) isLeaf;

- (NSArray*) allChildren;
- (NSEnumerator*) childEnumerator;
- (unsigned) childCount;
- (id) childAtIndex: (unsigned) index;

- (xxTree*) previousSibling;
- (xxTree*) nextSibling;
- (NSArray*) allSiblings;

- (void) addChild: (xxTree*) child;
- (void) addChild: (xxTree*) child atIndex: (unsigned) index;
- (void) removeChildAtIndex: (unsigned) index;
- (void) removeAllChildren;
@end


Now, apart from the fact we've mixed up the mutating and non-mutating methods (which, incidentally, CF does too), this is a much nicer interface to a tree than CF provides, and has some convenience methods CF doesn't have.  It can nearly all be coded internally as CF calls, though, which makes life much nicer - we don't have to reinvent the wheel.

So, now we need to make it identify itself to CF as the correct type.  Nothing simpler.  

    
- (CFTypeID) _cfTypeID {
  return CFTreeGetTypeID();
}


Simple as that.

OK, next up we need to make the class internals look right, so we can peek directly into the guts of the CF object.  To find this, we need to get the Darwin CF source, and simply grab the struct that defines the CF type - in this case it's __CFTree.

Looks like this...

    
struct __CFTree {
    CFRuntimeBase _base;
    CFTreeRef _parent;	/* Not retained */
    CFTreeRef _sibling;	/* Not retained */
    CFTreeRef _child;	/* All children get a retain from the parent */
    /* This is the context, exploded.
     * Currently the only valid version is 0, so we do not store that.
     * _callbacks initialized if not a special form. */
    void *_info;
    struct __CFTreeCallBacks *_callbacks;
};


Of course, we can't just use that.  We have to mess with it a bit.  Our internal structure has to look like this
    
typedef struct _xxTreeGuts {
    uint32_t _ignored;       /* ignore this */
    xxTree* _parent;	/* Not retained */
    xxTree* _sibling;	/* Not retained */
    xxTree* _child;	/* All children get a retain from the parent */
    /* This is the context, exploded.
     * Currently the only valid version is 0, so we do not store that.
     * _callbacks initialized if not a special form. */
    void *_info;
    struct __CFTreeCallBacks *_callbacks;
} xxTreeGuts;


You will notice that we don't bring across the 'isa' from CFRuntimeBase - objc_object already has one of those, remember.  The two 16 bit variables don;t interest us, so we create an ignored 32 bit variable and leave it at that (this is dangerous, as we might be omitting padding, probably best to leave 'em as two 16 bitters).

I'm precasting the CFTreeRef objects, makes it easier should we need to access them directly.

We also can't have any extra instance variable baggage to carry around, as CF methods will simply throw it away and we risk overwriting other objects if we access them through ObjC.  We need to be careful with all this, and the sizes need to be the same.

So our internal interface looks a bit like this, maybe.

    
@interface xxTree {
  xxTreeGuts _guts;
}
...
@end


Note that here, I'm using the structure as the entire contents of the object - this stops any unwanted optimisation happening. 

Then we override -init to throw away the allocated memory and allocate us a new object using the CF functions.  That way we're sure it's everything's kosher from the get-go.

And the last thing to do is patch up CF.  Actually, it's the first thing we do.  The _very_ first thing we do.

+load seems like a reasonable place for this, but as we can't guarantee CF will even exist at that time, we might well want to defer to +initialize.

The main problem with this is that we need to find the location in memory of a private variable.  We can find its address using 'nm', or even by parsing CF's mach-o structure ourself (it appears we have to parse the file, not the loaded instance), but these approaches take a little too much time.

The final options are *hardcoded' values for known versions of CF, and where that fails, a binary search through CF's data section, looking for a known sequence of bytes - we know the addresses of the bridged classes, and the order the bridged classes take, so that's not too arduous.

Ignoring the search part, the code to actually do the patching looks (a bit) like this:

    
+ (BOOL) bridgeClass: (Class) class toCFType: (CFTypeID) type {
  // Get the address of the class table
  NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
  struct objc_class ** cfClassTable;
  
  // dump out if we don't have CF yet
  #warning not here yet

  cfClassTable = (struct objc_class **)classTableAddress();

  // Check it's not already bridged
  struct objc_class * bridgingPointer = cfClassTable[type];
  if (bridgingPointer == class) {
    [NSException raise:NSInternalInconsistencyException
                format:@"Class %@ tried to bridge to CF type %d, to which it is already bridged",
                       NSStringFromClass(class),
                       type,
                       bridgingPointer->name];
  } else if (bridgingPointer != [NSCFType class]) {
    [NSException raise:NSInternalInconsistencyException
                format:@"Class %@ tried to bridge to CF type %d, which is already bridged to %s",
                       NSStringFromClass(class),
                       type,
                       bridgingPointer->name];
  }
  NSLog (@"Bridging user class %@ to CF type %d",
        NSStringFromClass(class), type);
  cfClassTable[type] = class;
  [pool release];
  
  return cfClassTable[type] == class;
}


Note this is in a separate class provided for the exact purpose of bridging CF to user classes ;-)

I will be releasing a few frameworks in the next week or so to provide exactly this functionality, and some more besides.  The stuff to do the patching, and a bridged CFTree is already there.

All at http://www.tufty.co.uk/Software, all BSD licensed.

enjoy

TufTy

----

You may need to be careful about retain/release, I'm not entirely clear on the issue. It appears to me at least that CF and Cocoa do not keep their RetainCount in the same place, so you may need to override the Cocoa retain/release code to use the CF equivalent. You may have already noticed this, of course. -- KritTer

----

OK, got it figured now.

First off, we need to get all the reference counting going through the same channels.  As we can't change the way that CFRetain() et al work, this means overriding -retain, -release and -retainCount in our classes to simply call the CF methods.

Next up, we need to be very careful what happens in our -init... methods.  As you may remember, we are delegating all responsibility for creating these objects to CF - so our -init... methods are throwing away the memory already allocated for the object and re-creating through CF.  The standard pattern for this is:

    
- (id) init {
  [self autorelease];
  [self = nil]; /* optional */
  // do real alloc and init here 
  return ....
}


However, in this case, this pattern causes an infinite loop.  Why?  Well, the loop occurs between CFRelease() and -release.  For some reason, CFRelease() is calling our -release method, which is calling CFRelease etc etc until we run out of stack space and eat flaming SEGV.

Back to the CF source, then, to understand what's going on.  In CFRuntime.c we find the definition of CFRelease (and CFRetain, etc).  And what we see is the following:

    
void CFRelease(CFTypeRef cf) {
    ...
    CFTYPE_OBJC_FUNCDISPATCH0(void, cf, "release");
    ....
 

So, there's the stuff that calls our release method.  But why?  Well, it's a macro that checks to see if the object in question is a 'pure' Objective-C object (i.e. unbridged).  And how does that work?  Well, what it comes down to is this:

obj-c runtime must be up.
object->isa != entry in __CFRuntimeObjCClassTable (for bridged classes, these will be equal)
object->isa != null

Now, as far as I can tell, for our uninitialised object, we should still be appearing as a bridged class, and thus autorelease should work, but it doesn't.  I still don't fully understand this.

However, replacing the autorelease with NSDeallocateObject(self) works 100%, so I'll leave it at that.  NSDeallocateObject is the function that gets called at the end of the -dealloc chain, and as we have no special handling for dealloc going on, we should be OK.

Just for kicks, I've overridden -dealloc to raise an exception (we should only be deallocing through CF anyway, so we might want to know if this being called.

This approach works, but **only** if we are willing to accept that we will never want to subclass and add instance variables.

Why?

Well, if we subclass on the ObjC side, we **want** our -dealloc chain to be called.  And that now can't happen.  On top of this, our standard -init method will in all probability SEGV, or at least overwrite random memory, as the 'base' initialiser will return a block of memory that is the size of the CF object, not the subclassed ObjC object, and any additional  variables will be outside that block.  Whoops!

So, we want our -init method to return the original block of memory, but with the 'base' part initialised correctly.  In addition, we want our dealloc method to get called.  How are we going to go about this?

First off, initialising.

**NOTE! FOLLOWING CODE (TO END OF PAGE) IS COMPLETELY UNTESTED AS OF 2 NOV 2002!**

Our current -init looks a bit like this:

    
- (id) init... {
  NSDeallocateObject (self);
  ...
  self = CFCreate....(stuff);
  return self;
}


But that's not going to work.  We need something like this:

    
- (id) init... {
  if (self = [super init]) {
    ...
    CFTypeRef myref = CFCreate...(stuff);
    // copy the CF object into self
    // delete the original CF object
  }
  return self;
}


Now, copying is a bit tricky.  We have two options.  

One is to use CF functions to copy (keeping all the internal retain counts up to date) and then simply CFRelease() the original, but this option isn't available for all CF types.

The other option is to simply memcpy() the object into our memory, then somehow release the original without breaking the retain counts.  this could be troublesome due to the way CF holds retain counts - it keeps the low 15 bits within the object (that mysterious rc part of CFRuntimeBase) and the rest in an external dictionary keyed on the address of the object.  We intend to change the address of the object, which could cause problems - luckily CF lazily initialises the dictionary, so we don't have to worry - the only problem we're likely to get is if we re-init an object with > 32,000 retains on it, so we won't be doing that.  So we can copy the object, but how do we then get rid of the original without releasing any internal stuff?  CFRelease() is out, but there is CFAllocatorDeallocate() which just deallocates the memory block.

So, that's that.  Our init looks a bit like this:

    
// Assume we have a struct myGuts which wraps all the CF stuff less
// the isa pointer
- (id) init {
  if (self = [super init]) {
    Class myClass = self->isa;  // Save isa
    ...
    CFTypeRef ref = CFCreate...(stuff);
    memcpy(self, ref, sizeof(struct myGuts) + sizeof(void*));
    self->isa = myClass;        // Restore isa
    CFAllocatorDeallocate(NULL, ref);
  }
  return self;
}


We have to save and restore our isa pointer, as the isa returned by the CFCreate... will return the base class, but we can't guarantee that's our actual class.

And now, we have problems.  Our -release and -retain methods won't work for subclasses, as they have different classes to the 'base', and thus CFRelease/CFRetain will call their ObjC equivalents as described before, and it'll be back to SEGV City via Infinite Loop Central...

So, what should they look like?

    

- (id) retain {
  Class myClass = self->isa;     // Save actual isa
  self->isa = [BaseClass class]; // replace temporarily
  CFRetain (self);
  self->isa = myClass;           // And restore it
  return self;
}

- (oneway void) release {
  Class myClass = self->isa;     // Save actual isa
  BOOL shouldDealloc = ![self retainCount];
  
  if (shouldDealloc) {
    [self dealloc];  // Need to call custom dealloc before possibly releasing the object
  }
  self->isa = [BaseClass class]; // replace temporarily
  CFRelease (self);
  if (!shouldDealloc)
    self->isa = myClass;         // And restore it if we didn't dealloc
}

- (unsigned) retainCount {
  Class myClass = self->isa;     // Save actual isa
  unsigned rc;
  self->isa = [BaseClass class]; // replace temporarily
  rc = CFGetRetainCount (self);
  self->isa = myClass;           // And restore it
  return rc;
}

- (void) dealloc {
  // Deallocate any additional memory we own.
  NSZoneRealloc([self zone, self, sizeof(struct myGuts) + sizeof(void*)]);
  // Don't call [super dealloc];
}


And that, I think, should do it.  We trick the CF runtime into not treating our subclasses as pure ObjC classes, we call our custom dealloc chain, we get rid of all our additional memory.

It should probably be noted that subclassing bridged classes will blow away the bridging ability to a certain extent - (CFTypeRef)myObject will work, but the converse won't, in the same way that (NSObject*)myObject will work, but (MyClass*)[[NSObject alloc] init] won't.  But that's bleedin' obvious.

TufTy

----

Rather bemused. Why are you copying the new CF object into the original Cocoa object? Why not just return the CF one?

    
// Assume we have a struct myGuts which wraps all the CF stuff less
// the isa pointer
- (id) init {
  Class myClass = self->isa;  // Save isa
  CFxxxRef ref = CFxxxCreate(stuff);
  [self release];
  self = (id)ref;
  self->isa = myClass;        // Restore isa
  return self;
}


That way, when the retain count hits zero, you don't need to worry about the fact that CFRelease is going to try to deallocate the pointer it's given (which your code doesn't seem to deal with):

    
- (id) retain {
  Class myClass = self->isa;     // Save actual isa
  self->isa = [BaseClass class]; // replace temporarily
  CFRetain (self);
  self->isa = myClass;           // And restore it
  return self;
}

- (oneway void) release {
  Class myClass = self->isa;     // Save actual isa
   self->isa = [BaseClass class]; // replace temporarily
  CFRelease (self);
  self->isa = myClass;         // Errr...whoops?
}

- (unsigned) retainCount {
  Class myClass = self->isa;     // Save actual isa
  unsigned rc;
  self->isa = [BaseClass class]; // replace temporarily
  rc = CFGetRetainCount (self);
  self->isa = myClass;           // And restore it
  return rc;
}


Except the code is still illegal when the dealloc occurs. Excuse me while I go and ponder...

No, I have no quick solution. And now I have to go and work. Excuse me again, and good luck.

 -- KritTer
 
 ----
 
 Reason for copying is because I might want to subclass the class and add extra variables.  If I simply return the CF stuff, I don't get the extra memory.  A bit like wanting to use an NSMutableArray, but expecting [[NSObject alloc] init] to provide me everything I need.
 
 Yeah, it means jumping through lots of hoops.  And I've still got all sorts of problems with it, but it's almost working.
 
 TufTy

So make it a class cluster like all the other bridged stuff. I just can't see how you could get around the whole memory deallocation problem. -- KritTer

----
Forgive the obvious question, but if I wanted to make a subclass of a class that is toll-free bridged, but still wanted to retain the bridging, how could I add an interface variable?

