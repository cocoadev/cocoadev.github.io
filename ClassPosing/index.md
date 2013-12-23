---
layout: page
title: ClassPosing
---



NSObject's poseAsClass: method lets you take a class of your own and use it to replace an existing class.  This is useful for changing behaviour of an existing class.

For example, let's say you create a subclass of NSWindow (we'll call it MyWindow) that draws its elements (title bar, close/minimize/zoom buttons) differently from the Aqua standard.  You can already use IB to specify the class of a window object and make it use your subclass, but what if you want your subclass to be used EVERYWHERE in your application, without modifying all your nibs (perhaps you allow a user preference to specify the look of the windows)?  poseAsClass: will let you insert MyWindow into the object hierarchy in place of NSWindow;  All NSWindow subclasses will now effectively be subclasses of MyWindow.  Interesting, MyWindow's superclass will still be the original NSWindow, even though all external accesses to NSWindow will now access MyWindow instead!

**Example:**
A piece of the class hierarchy before calling poseAsClass:

    
 NSObject -- NSWindow -- NSPanel
                      \- MyWindow


after calling [MyWindow poseAsClass:[NSWindow class]]

    
 NSObject -- NSWindow -- MyWindow -- NSPanel


The important distinction that is not seen in the "after" diagram is that from now on, the original NSWindow can no longer be referenced by name;  It still exists and its code will be used wherever MyWindow refers to "super", but it is "invisible" and all references to NSWindow will actually go to MyWindow.

For this method to work best, it's important to do the posing as early as possible when the application starts;  Inside of the main() function is a good place.

See also PosingClasses.

StepWise article on posing (and ClassCategories, and when to use each): [http://www.stepwise.com/Articles/Technical/PosersAndCategories/index.html]

----

So, it seems like posing as a class only guarantees that new objects of that class with use the poser class, and that existing objects won't be converted.  Has anybody else found this to be true? (or false?) has anybody else encountered this behaviour?

----

As stated by the second to last sentence of the opening entry.

	*For this method to work best, it's important to do the posing as early as possible when the application starts;  Inside of the main() function is a good place*

----

The diagrams above may be the best way to understand it at first, but they aren't exactly correct.  The hierarchy goes from 

    
 NSObject -- NSWindow -- NSPanel
                      \- MyWindow


to

    
 NSObject -- %NSWindow -- NSWindow -- NSPanel
                       \- MyWindow


    NSWindow is mostly a shallow copy of     MyWindow, except that it has a different name.      %NSWindow is the class formerly known as     NSWindow.  It can no longer be referred to by name.

    MyWindow and     NSWindow (post-posing) are distinct classes, but they share exactly the same method lists, adopted protocols, etc. 



----

A class can only pose for its super class if it has no instance variables, but there's a standard workaround: a static NSMutableDictionary mapping instances to 'ivar' values.  It's the same trick used for ClassCategories, and there's sample code over there.

----

Hypothetical question: what happens if two classes try to pose as the same class. Would the class hierarchy then look like this? --JediKnil
    
 NSObject -- %%NSWindow -- %NSWindow -- NSWindow
                        \- WindowOne \-WindowTwo


----

Pretty much.  If you look at the strings the runtime print out, it's actually
    
 NSObject -- %NSWindow -- %NSWindow -- NSWindow
                       \- WindowOne \-WindowTwo


    %NSWindow isn't a class that can be referenced by name anyway, so, uh, I guess it doesn't matter that two classes have the same name.

Note something weird:  post posing,     [MyWindow isKindOfClass:[NSWindow class]] is false.

----

I did some playing around with poseAsClass: and just wanted to share the code with you. It seems method IMP caching could get you into trouble in some rare cases (see code comments). 
    
 #import <Cocoa/Cocoa.h>
 
 //INTERFACES
 @interface Master: NSObject
 -(void) shout;
 -(void) whisper;
 -(void) talk;
 @end
 
 @interface Slave: Master
 -(void) whisper;
 @end
 
 @interface Imposter: Master
 -(void) shout;
 -(void) whisper;
 @end
 
 
 //MASTER
 @implementation Master: NSObject
 - (void) shout{
   printf("MASTER: SHOUT\n");
 }
 - (void) whisper{
   printf("MASTER: whisper\n");
 }
 - (void) talk{
   printf("MASTER: talk\n");
 }
 @end
 
 //SLAVE
 @implementation Slave: Master
 - (void) whisper{
   printf("SLAVE: whisper\n");
   [super whisper];
 }
 @end
 
 //IMPOSTER
 @implementation Imposter: Master
 - (void) shout{
   printf("IMPOSTER: SHOUT\n");
   [super shout];
 }
 - (void) whisper{
   printf("IMPOSTER: whisper\n");
   [super whisper];
 }
 @end
 
 
 //MAIN
 int main(void) {
   Slave* slave;
   
   slave = [Slave new];
   
   /* if you uncomment the next statement the first call to shout after the       */
   /* poseAsClass: call will still use Master's shout implementation. The second  */
   /* call (just before return) will use Imposter's implementation however. Some  */
   /* caching and cache-refreshing seems to be going on here                      */
     
   //[slave shout];
   //printf("--\n");
   
   
   /* if you uncomment the next statement posing of Imposter as Master will       */
   /* work as expected for all method calls                                       */
       
   //[slave whisper];
   //printf("--\n");
   
   [Imposter poseAsClass: [Master class]];
   
   
   [slave shout];
   printf("--\n");
   
   [slave whisper];
   printf("--\n");
   
   [slave talk];
   printf("--\n");
   
   [slave shout];
   
   return 0;
 }

--GregorNobis

*Why don't you use NSLog? This is Cocoa! --JediKnil*

*Well, for me it's not pure Cocoa, I'm working on a project that mixes C, Objective C and Scheme. I know, I could've still used NSLog - but it doesn't seem to make much of a difference. I just wanted to print out a couple o' words and printf did the job. --GregorNobis*

----

I tried this with XCODE 1.5 (ZeroLink) - and it didn't work (using Developer option). However, I found if I used the "Deployment" option, it worked. So, just be aware you might have the same problem. I was trying to pose as NSTimer, and after I did the poseAsClass I put NSLogs to look at the classes - that is how I found the problem.

David

