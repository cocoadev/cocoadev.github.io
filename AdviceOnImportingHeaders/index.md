---
layout: page
title: AdviceOnImportingHeaders
---



I'm confused as to how to properly include all the headers in my project to make everybody happy.

I have 2 classes that need to know about each other, but all hell breaks loose if they try to include each other's header files via #import. One is a simple application class based on NetSocket example, GhettoServer.  I put @class ClassName in it to make it happy but it produces warnings when it calls methods to that class. Importing the header will blow it up with parser errors.

It seems to be that the PSApplication class imports a header, which imports another header, and eventually gets to another class that's doing @class PSApplication so it knows about PSApplication being defined.  

----

Well, as a general rule, *never* include headers in your headers **unless you are inheriting from the class that you are including**.
If you need to include an object as an interface variable, use the @class directive since it will tell the compiler that the identifier refers to a class.

**Import the headers of the classes you are using only in your implementation files.**

This way, the compiler knows that your instance variables are pointers to objects but doesn't know the details of the object when parsing the header (since all it needs to know is that it is a class) and the compiler can see the methods of the class when parsing your implementation file (since it needs the class at that point to verify that it does respond to the messages you are trying to send).

Does that help?

--JeffDisher

----

Well said Jeff, I total agree. NEVER include/import headers in your class headers (except the header for the class you are inheriting from as Jeff mentioned). Always use the @class directive. If a class header is defining structs, I would recommend moving all type defining stuff to a generic header not associated with any class interfaces. If a class header also includes a protocol, I would also recommend moving all protocol declarations into a separate header to avoid preprocessing headaches. 

--zootbobbalu 

----

*By way of example*

For my first project I would subclass NSButton and add code to make it bounce around the screen.  So far, everything has been going pretty well.  I can take a NIB, add a new NSButton (I use the round ones with no text), change the class to DButton, draw a connection to the NSView and compile and it will bounce like mad.  I know this is a vastly inefficient way to do this, but I thought it would be cool.

For the next part, I am writing a collision detector that keeps track of all the balls on the screen and lets them know if they need to bounce.  I have DButton register itself with collisionDetector at init.  Collision detector stores all the buttons in a NSMutableArray. DButton stores it's index as an int and passes it to this function:

    
 - (BOOL)checkCollision:(int)sender
 {
     int i;
     NSRect view1, view2;
     DButton *ball1;
     DButton *ball2;
     
     // some variables removed for space
 
     view1 = items objectAtIndex:sender] frame];
     
     for(i=0; i<[items count]; i++)
     {
         view2 = [[items objectAtIndex:i] frame];
 
         if(!(i == sender))
         {
             
             // a bunch of working code removed...            
            
             if( ((top || bottom) && (left || right)) )
             {
                 ball1 = [items objectAtIndex:sender];
                 ball2 = [items objectAtIndex:i];
                 
                 m1 = [ball1 mass];  // here is the problem
                 m2 = [ball2 mass];  // "warning: `DButton` does not respond to `mass`"
                 
 		// figure out the velocities
 
                 [[items objectAtIndex:sender] setVelocityVx:v3x Vy:v3y];
                 [[items objectAtIndex:i] setVelocityVx:v3x Vy:v3y];
 
                 return TRUE;
             }
             top = bottom = left = right = FALSE;
         }
     }
     return FALSE;
 }


I get errors at the part where I set m1 and m2, saying DButton does not respond to 'mass' when in fact, it does.  I think I am having a problem understanding what is happening with the [[NSMutableArray.

Please let me know what you think, or if you need more information.  Thanks in advance.

--DerekCramer

----

Only thing that comes to the top of my mind right now is that you either didn't #import "DButton.h", or you didn't declare -mass in DButton.h or possibly it either wasn't implemented in DButton.m or you misspelled the method name in either file. --KevinPerry 
----
I thought that too, but I checked most of that, here is my header files:

    
 /* DButton */
 
 #import <Cocoa/Cocoa.h>
 #import "CollisionDetector.h"
 
 
 @interface DButton : NSButton
 {    
     double iteration;
     double timeInterval;
     double vx, vy;
     double mass;
     float g;
     NSRect previous;
     double prevyv, prevxv;
     IBOutlet NSView *window;
     IBOutlet CollisionDetector *collisionDetector;
     int collisionID;
     
 }
 - (IBAction)moveDown:(id)sender;
 - (IBAction)moveLeft:(id)sender;
 - (IBAction)moveRight:(id)sender;
 - (IBAction)moveUp:(id)sender;
 - (void)move;
 - (float)time;
 - (double)mass;
 - (void)setVelocityVx:(float)velx Vy:(float)vely;
 @end
 
 /* CollisionDetector */
 
 #import <Cocoa/Cocoa.h>
 
 @class DButton;
 
 @interface CollisionDetector : NSObject
 {
     NSMutableArray *items;
 }
 - (int)addView:(id)sender;
 - (BOOL)checkCollision:(int)sender;
 @end


*But did you     #import "DButton.h" in the file that codes     -(double)checkCollision?*

----

To be honest I am a little stumped on when I need to import and when I don't need to.  I assumed that because I     #import "DButton.h" in CollisionDetector.h, I don't need to import it in CollisionDetector.m. --DerekCramer

*From the code you posted above, you used     @class DButton;, not     #import "DButton.h". This is probably where you got confused.*

----

To help a little, @class is used to forward-declare a class. It says "This thing is a class" but doesn't tell the compiler anything else about it. So for useful code you need to import the header file.

With classes, you almost always want to import only in the .m file and forward-declare in the header. This keeps weird import loop/collision problems from arising and saves you headache while simultaneously making your header files cleaner and warmer, too!

Okay, seriously though, it's good practice. -- RobRix

*Another good practice is to declare a FormalProtocol that your class will use and just write your code around that; then you never need use the classname at all, and your code becomes more flexible if e.g. you want to reuse it with a similar class. Experience (and some thought) will help you decide when to use this approach. (If you need to create an object, a FormalProtocol won't help you.) -- KritTer*

----

**Here's another example to urge you not to be shy about importing headers of ANY necessary classes into your class implementations**
(Moved from a now-defunct (but too-generally-titled) page that was called WarningMessages)

I have two classes that have pointers to elements of each other in them.  Because of this I need to use the forward declaration syntax @class in one of them.  The files would look something like this:

    
 #import "Bclass.h"
 
 @interface Aclass : NSObject
 {
     Bclass *b;
 }
 
 - (void)method1;
 
 ----
 
 @class Aclass;
 
 @interface Bclass : NSObject
 {
     Aclass *a;
 }
 
 - (void)method2;

----

This works fine in execution, but the compiler generates a warning message in every instance of this code in the 'Bclass' implementation:

    [a method1];    <--- ! 'Aclass' may not respond to '-method1'
                                  - ! cannot find method '-method1:'; return type 'id' assumed

In the Aclass implementation:

    [b method2];

Generates no warning messages because the header is imported at compile time.

Is there any way I can strip down the warning messages so that only the ones I want to see are there?

----

Although you are not #importing the classes in the .h file, you still must #import the classes into both .m files. This will shut up the warnings and also prevent some other potential problems.

----
I've got a similar problem, actually. I've got a subclassed NSSplitView, named "FTPSplitView". I have a few methods in there (mainly restoreDefault: and saveDefault) for maintaining the splitview position. In the class I call from, AppController, in my main file (AppController.m), I call     [SplitView restoreDefault:@"MyDefault"]; , and even with #import "FTPSplitView.h" in the .m file, it still gives me "NSSplitView may not respond to -restoreDefault:" 

The actual execution works perfectly, just those silly warnings...

Any ideas? 

--Brian Amerige

----

This has nothing to do with headers. Read the error message again. The compiler things the split view is an NSSplitView, not an FTPSplitView. This means that the type of your variable is incorrect. Fix the type and your warnings should go away.


----

Thanks! I totally forgot to change the in my AppController header file to FTPSplitView, as it had been there as NSSplitView. Thanks for the heads up.

----
As you can use @class A and tell the compiler that 'there is a class A'  and avoid including headers, is there any way to forward declare the methods to remove the warnings?

----
Yes, write a category interface which contains the methods, and put it somewhere visible to the code that uses the methods. It's much better to include the real header if you can do it, though.

----
Also, bear in mind that the *order* in which you import your headers is important.  If you are forward declaring with @class, and importing in your .m, and are still getting build errors, then check the order in which you are importing those headers in .m.  If class A references class C, be sure to import class C's header somewhere above class A's.  This might be the source of some of the troubles people are running into.

