---
layout: page
title: SingletonDesignPattern
---



If you're writing a class and want to ensure that there is never more than one instance of this class, and that there is a global access point to this one instance, then use the Singleton design pattern.  For more information on this design pattern, see *Design Patterns*, page 127.

Apple recommends the following method of creating a singleton:

<syntaxhighlight lang="objc">
static MyGizmoClass *sharedGizmoManager = nil;

+ (MyGizmoClass*)sharedManager
{
    @synchronized(self) {
        if (sharedGizmoManager == nil) {
            self alloc] init]; // assignment not done here
        }
    }
    return sharedGizmoManager;
}
 
+ (id)allocWithZone:([[NSZone *)zone
{
    @synchronized(self) {
        if (sharedGizmoManager == nil) {
            sharedGizmoManager = [super allocWithZone:zone];
            return sharedGizmoManager;  // assignment and return on first allocation
        }
    }
    return nil; //on subsequent allocation attempts return nil
}
 
- (id)copyWithZone:(NSZone *)zone
{
    return self;
}
 
- (id)retain
{
    return self;
}
 
- (unsigned)retainCount
{
    return UINT_MAX;  //denotes an object that cannot be released
}
 
- (void)release
{
    //do nothing
}
 
- (id)autorelease
{
    return self;
}
</syntaxhighlight>

http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/CocoaObjects.html%23//apple_ref/doc/uid/TP40002974-CH4-SW31 (bottom of the page, slightly different implementation)

Attempting to write your own singleton implementation is risky, especially considering the problems of multi-threading, and possibly of Cocoa shifting under you. Since Apple has officially sanctioned this code, it is likely to remain valid indefinitely.

----

If I want to have the instance of a Singleton class in my NIB file, is it safe to change allocWithZone: to return [NSNull null] so that the class can safely be stored inside an NSArray (as the NIB would do). Or should I just pragmatically do whatever I need to do with the class and the GUI? -- ACoolie

----
No.
Don't store singletons in nibs.  It is a path to disaster.  If you want to make conenctions to a singleton within IB, use key-paths through a know place holder object like File's Owner or use the singleton as the File' Owner or use the responder chain.

----
Could you elaborate? I have used singletons in nibs by overriding     -init to return the singleton without any trouble. -- MikeAsh

----
I used to use the Singleton class that came as a file template with TextMate. All that class does is in -init, if there is already an instance of the class, it will release the current instance and return the shared instance. That class has always worked fine in NIBs but I figure it is probably better to use the Apple-sanctioned code. 
I have a basic AppController that has an instance variable that is an array of dictionaries that is used to populate an NSPopUpButton. There can be multiple documents open at once so I want the NSPopUpButton in each document to have the same values. 
Normally I would just put an instance of the singleton class in the NIB and bind the button but I get an error about inserting nil into an NSArray. When I remove the shared instance from the nib, the error goes away. I tried changing -allocWithZone: to return [NSNull null] instead of nil so it could be used in an array but then I get an error saying it cannot create an array out of <null>. 
Doing all of the above in code instead works fine if I give the NSObjectController the shared instance for content. -- ACoolie

----
In order to work within a nib,     [[SingletonClass alloc] init] must return a functioning object. The code here returns nil, which is why you get this error. Returning NSNull makes no more sense. My code in this case merely checks if the singleton exists, and if it does, it does a     [self release] and returns the singleton. Works fine, and I don't see why this is a "disaster'. -- MikeAsh

----
Potential Problems:

The question seems to be "Can I used code like the following to implement a singleton AND be able to instantiate the singleton class in an Interface Builder nib file?"
<syntaxhighlight lang="objc">
- (id)init
{
   static id          sharedInstance = nil;

   if(nil == sharedInstance)
   {
      sharedInstance = [super init];
   }
   else
   {
      [self release];
   }
   return sharedInstance;
}
</syntaxhighlight>

Answer:

1) Top level objects in a nib are released by NSWindowController in certain circumstances.  You must very carefully implement your singleton class's -init to retain the object returned or the one and only instance will be dealloced the first time a NSWindowController releases the nib containing the singleton.  So, at a minimum, the code should be

<syntaxhighlight lang="objc">
- (id)init
{
   static id          sharedInstance = nil;

   if(nil == sharedInstance)
   {
      sharedInstance = [super init];
   }
   else
   {
      [self release];
   }
   return [sharedInstance retain];  // Note critical retain!
}
</syntaxhighlight>

An alternative recommendation (from Apple) is to implement the following in the singleton class:
<syntaxhighlight lang="objc">
- (id) retain { return self;}
- (id) copy { return self;}
- (unsigned int) retainCount { return UINT_MAX; }
- (void) release { }
</syntaxhighlight>

However, if you combine Apple's recommendation with the idea of releasing self in -init, there is a problem:  Apple's code turns -release into a no-op so that the releasing self in init is a no=op and every instance after the first is just leaked.

Things are only just starting to get complex...

2) Even if you are not using NSWindowController, you are supposed to eventually release all top level objects in any nib you load.  The concept behind nibs is that you can load and reconstitute the archived objects in the nib.  Using a singleton in a nib subtly breaks that contract.  Frankly, the whole idea of INSTANTIATING a singleton in a nib is conceptually broken.

3) In many cases, you need to implement -encodeWithCoder: and -initWithCoder: methods for an object in a nib to work correctly.  How will your singleton sensibly implement them ?

4) If the singleton is ever put in an IB palette, there will only be one instance within the IB process which means changes in one nib will affect other nibs open at the same time.  That subtly breaks editing of multiple nibs at once and poor hapless users will have no idea what is going on.

5) If your singleton happens to be a subclass of NSCell or possibly some other classes, using the instance in a nib may require you to implement the NSCopying protocol.  Can you sensibly do that for a singleton ?

----
1) This is just saying that you must follow proper MemoryManagement, which I would hope is obvious.

2) "Release" is not the same as "destroy". You do reconstitute the objects in the nib, I see no problems here.

3) Singletons are invariably controller objects. As such, they live at the top level of the nib, not in the view hierarchy, and have no need of NSCoding.

4) Allowing the configuration of a singleton in IB is probably the wrong thing to do regardless.

5) If you have a singleton view class then you're already doing something badly wrong, talking about the sensibilities of individual scenarios is entirely missing the point.

6) Apple does it. "To get your application to use your custom subclass of NSDocumentController, you must ensure your subclass is the first instance of NSDocumentController created when the application starts up. There are two ways to do this: 1.Create your subclass in the main nib file." Other Apple singletons such as NSFontManager exist fine within nibs too.

-- MikeAsh

----
So it would be safe to copy the default TextMate singleton and in -init check if there is already a shared instance, and if so release the current instance and return the shared one? And if i do so the singleton would work fine in a nib? -- ACoolie
----
I haven't seen the default TextMate singleton, but if it doesn't either 

A) Retain the singleton again every time before returning from -init

or

B) implement 
<syntaxhighlight lang="objc">
- (id) retain { return self;}
- (id) copy { return self;}
- (unsigned int) retainCount { return UINT_MAX; }
- (void) release { }
</syntaxhighlight>

BUT NOT BOTH then it is absolutely NOT safe to copy the default TextMate singleton and instantiate it in a nib file.
----
I don't understand why you say "but not both", or why you felt it was so important that it had to be put in ALL CAPS. Doing both means that the retain in -init simply does nothing, and is therefore harmless. This is the sort of "belt and suspenders" approach which I have come to associate with good programmers and is certainly not something that deserves a shouted warning against it. -- MikeAsh

If I read it correctly doing both would cause a memory leak due to the [self release] in the init become a noop.

----
Good point, you'd need a     [super release]. However, B by itself is never adequate for nib use, so the comment is still puzzling. -- MikeAsh

----
No-one has mentioned overriding     -dealloc, of course it shouldn't be directly invoked but if through some accident/nefariosity it is, shouldn't it do something like:
<syntaxhighlight lang="objc">
- (void)dealloc
{
  sharedGizmoManager = nil;
  [super dealloc];
}
</syntaxhighlight>
so that subsequent uses of     +sharedGizmoManager do something useful? -- GrahamLee

----
No one has mentioned     -dealloc because a singleton should never be dealloced. Setting the shared instance to nil is very wrong, as it may be in use.

----
Because I think this is an important issue to get right, and people will come back to this page, and Apple's code may be dangerous, let me add my fixed version of Apple's code that I am convinced is safe to use. It allows instantiating the singleton in a nib and moreover allows custom initialization. -- ChristiaanHofman

<syntaxhighlight lang="objc">
static MyGizmoClass *sharedGizmoManager = nil;

+ (MyGizmoClass*)sharedManager
{
    @synchronized(self) {
        if (sharedGizmoManager == nil) {
            self alloc] init];
        }
    }
    return sharedGizmoManager;
}

+ (id)allocWithZone:([[NSZone *)zone
{
    @synchronized(self) {
        if (sharedGizmoManager == nil) {
            return [super allocWithZone:zone];
        }
    }
    return sharedGizmoManager;
}

- (id)init
{
    Class myClass = [self class];
    @synchronized(myClass) {
        if (sharedGizmoManager == nil) {
            if (self = [super init]) {
                sharedGizmoManager = self;
                // custom initialization here
            }
        }
    }
    return sharedGizmoManager;
}

- (id)copyWithZone:(NSZone *)zone { return self; }

- (id)retain { return self; }

- (unsigned)retainCount { return UINT_MAX; }

- (void)release {}

- (id)autorelease { return self; }
</syntaxhighlight>

----

I've written up an in-depth discussion of the singleton pattern here:

http://www.duckrowing.com/2010/05/21/using-the-singleton-pattern-in-objective-c/

