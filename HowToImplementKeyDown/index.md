---
layout: page
title: HowToImplementKeyDown
---

I am trying to implement keyDown in my custom view class.  I put the following code in the implementation file...

    
-(void)keyDown:(NSEvent *)event
{
     NSLog(@"You just pressed a key");
}


But, when I run the program and hit a key, it beeps and does not log anything.  I connected the custom view to the window it is in so that it is the initialFirstResponder.  Does anyone know why this is not working?  I also tried to change the word "event" to "theEvent", but this did not seem to help.  Any suggestions? --AlexanderD

**

Did you remember to set the delegate of the view to your class?  Do any other delegate methods work?
-Gumpan

**

Gumpan,

I figured it out.  What surprised me was that the mouseDown method worked fine, so what was wrong with keyDown?  Well, it turns out that I did not know you have to call the function

    
-(BOOL) acceptsFirstResponder
{
     return YES;
}


Now I know.  Cocoa can be strange. --AlexanderD

----

I agree Cocoa can be strange, but this detail isn't especially so. Now class clusters -- that's strange!

Anyone interested in handling events (rather than simple NSControl actions) should read the documentation on NSResponder and the event handling system carefully. The idea that the event system needs to know who is interested in receiving events is common to most, if not all, application frameworks.

-- MikeTrent

----

Agreed in entirety, Mike; I would, however, like it if Apple's class references had some transparency to them so you could see inherited methods in there with them. *sighs* Joe, hurry up with OCB! I'll have Emily interpret code and provide documentation in v2 ;) -- RobRix

Uh, you mean like clicking on the inheritance line in the documentation? Or cmd-clicking on class names? Or looking at the class browser? -- MikeTrent

None of the above. I mean like having the class reference .html files show inherited methods in them instead of having to go to the superclass' page. -- RobRix

ewww -- MikeTrent

Whatever. I would personally absolutely love it with the AppKit and the mutable classes. -- RobRix

Well, ObjCBrowser will be handy.  (:  It actually already works and exists... the functionality Rob Rix refers to may be OCB's ability to view HeaderDoc(not implemented yet) and the ability to easily browse through a class hierarchy, methods, etc using a neato view(Aren't familiar with it?  See <AppKit/NSNeatoView.h>).  As for the rest... I'll take a page from Apple's documentation and deftly put it here, in its entirety:  Description forthcoming.  --JoeOsborn

