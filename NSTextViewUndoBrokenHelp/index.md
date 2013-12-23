---
layout: page
title: NSTextViewUndoBrokenHelp
---

In my application I have a text view that exhibits some strange behavior.

If I open a file and add to the end of it, the red "unsaved changes" dot appears and everything is fine. If I save the file and add more text to the end of the file, the dot does not appear and the user is not prompted to save changes when closing the window. Adding text before the end of the file always causes the red dot to appear.

I am not doing anything with Undo and the text view - I just checked off the option for "Allows Undo" in IB. I am able to recreate the problem by creating a brand new app composed of a single text view and providing a very simple dataRepresentationOfType method. I originally suspected it was a problem with my laptop, but it has the same problem on other machines as well.

Has anyone else seen this behavior? Or have any ideas about how to debug this?

thanks in advance.

----

I started a brand new project from scratch with nothing but a text view and a dataRepresentationOfType: method and I still see the same behavior. This seemingly rules out corrupted NIBs, anything to do with text view delegates, etc. I can also copy the app to a friend's system and see the same result, ruling out a local configuration issue. I am baffled. How do you debug the automatic undo in a text view?

----

Does this sound like what you're seeing: [http://www.cocoabuilder.com/archive/message/cocoa/2002/3/27/69841]

You could also set the undo manager and/or document's state yourself when you need to.

