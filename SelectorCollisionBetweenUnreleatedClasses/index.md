---
layout: page
title: SelectorCollisionBetweenUnreleatedClasses
---



I'm brand new to the Objective-C platform, and I've run into the following error that has really left me scratching my head, and I'm pretty sure that I'm missing something fundemental.  So I've put together the following example that produces the problem (this was done with Xcode 2.4 on 10.4.10)

    
-- Model.h --
#import <Cocoa/Cocoa.h>
#import "Part.h";

@interface Model : NSObject {
}

- (Model*)initWithName:(NSString*) theName color:(NSColor*) theColor;
- (void)addPart:(Part*) part;

@end
---

-- Model.m --
#import "Model.h"

@implementation Model
- (Model*)initWithName:(NSString*) theName color:(NSColor*) theColor {
    self = [super init];
    return self;
}

- (void)addPart:(Part*) part {
}

@end
---

-- Part.h --
#import <Cocoa/Cocoa.h>

typedef enum { RED, GREEN, BLUE } IndexedColor;

@interface Part : NSObject {

}

- (Part*)initWithName:(NSString*) theName color:(IndexedColor) colorIndex;

@end
---

-- Part.m --
#import "Part.h"

@implementation Part

- (Part*)initWithName:(NSString*) theName color:(IndexedColor) colorIndex {
    self = [super init];
    return self;
}

@end
---

-- elsewhere --
  void test() {
->    Model* m = Model alloc] initWithName:@"foo" color:[[[NSColor blueColor]];
      [m description];
      Part* p = Part alloc] initWithName:@"bar" color: 1];
      [m addPart: p];
  }
--


Compiling this produces the error...

error: incompatible type for argument 2 of 'initWithName:color:'

... on the line in the test() function marked with an arrow.  Near as a I can tell after playing this for a while is that the compiler has somehow applied the definition of the selector 'initWithName:color' for Part to Model.

I'm having a hard time believing that I have to worry about selector name collisions between entirely independent classes.  What am I missing here?

----

You want your initWithName:color: methods to return id, not specific classes:
    
-(id) initWithName:([[NSString *)name color:(NSColor *)color


----
While that's true, it's not the problem. (And, it's just a convention; Cococa programs work perfectly fine if you use a static type for     -init methods.)  --JediKnil

----
It does this because     +alloc returns id, and therefore the compiler doesn't know which init method to use, so it picks one arbitrarily. It'll pick the same one arbitrarily each time, so one of the two message sends in this code will be wrong.

The general answer to this is that if your type signatures do not match, your selector names should not match either. In this case I would rename the second init method to     initWithName:indexedColor: which will not only solve the error and allow the compiler to better check your code, but it will also make the programmer much less prone to mistakes in coding, especially in the special case of mistakenly passing 0 to the NSColor method which will be interpreted as nil with no warnings. -- MikeAsh

----

To clarify: The underlying problem is that when the Objective-C compiler can't tell the specific type of the receiver of a message, and the same selector is defined multiple times using different argument types, the compiler has to just pick one of the types.  Sometimes it picks wrong type and then complains that there is a type mismatch.  Objective-C doesn't know the specific type you are using because +alloc returns the id type.

There are two solutions:

1) Tell the Objective-C compiler the specific type with a cast:
    
  void test() {
      Model* m = [(Model *)[Model alloc] initWithName:@"foo" color:[NSColor blueColor]];
      [m description];
      Part* p = [(Part *)[Part alloc] initWithName:@"bar" color: GREEN];
      [m addPart: p];

      [m release];
      [p release];
  }


2) Don't define same selector with multiple different argument types
    
@interface Part : NSObject {

}

- (Part*)initWithName:(NSString*) theName indexedColor:(IndexedColor) colorIndex;

@end

  void test() {
      Model* m = Model alloc] initWithName:@"foo" color:[[[NSColor blueColor]];
      [m description];
      Part* p = Part alloc] initWithName:@"bar" indexedColor: GREEN];
      [m addPart: p];

      [m release];
      [p release];
  }


----
Thank you for the elaboration, masked stranger. You probably saved some head scratching.

----

Thanks for the detailed explanations guys!  I'm on the right track now.  The explicit return types from the init methods were the result of my trying to solve this problem by being more explicit about the types involved, but the thing I was missing was alloc returning an id.  I think I'll stick with explicit types for now and see how they treat me.  Based on your explanation, I was able to figure out another bogus selector problem I was having.  I must say while I'm intrigued by the Objective part of this language, the C part is giving me a rude reminder of what a hairpulling experience C programming was (and is). - Wilhelm

----

Well, I think you've reasoned yourself in the wrong direction.  You should probably stick to the standard of returning an     id from     [-init].  Class clusters are a good reason for this behavior (a bit of an advanced topic, but an important one nonetheless).  In fact, by declaring a static return type for     [-init], you'll be doing the following, which is semantically invalid:

    
@implementation [[MyClass // a subclass of... well, anything, really

- (MyClass)init
{
  return [super init];
}

@end


Think about it.  There's absolutely no reason this shouldn't work, as the most generic possible initializer.  And, of course, it's not going to cause any invalid behavior, but it just doesn't make sense, and will generate a warning.  Typically, it's always a good idea when learning a new language to stick to standard behavior.

