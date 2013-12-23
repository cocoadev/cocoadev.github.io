---
layout: page
title: TemporaryFocusGrab
---

Hey

I am not sure I am asking the correct thing here (terminology wise), but I was woundering if there is a way that I could temporarily grab a View's focus. Now, in case I am using the wrong term, I am playing around with some core image transitions. I have seen some nice examples all over the place and am writing my own library. One problem I have noticed however is when I go to capture a screen shot of the view I am about to change (both before an after) that one of the views has lost focus, so things like scroll bars which are usually blue with focus are now gray. To solve this other libraries/frameworks have simply provided helper functions in which you can add the initial and final images at different times. So for example, one library I have seen goes:


    
    General/NSView *initialContentView = General/self selectedTabViewItem] view];
    [[NSView *finalContentView = [tabViewItem view];

    // Render the initialContentView
    [transition setInitialView:initialContentView];

    // Invoke super's implementation of -selectTabViewItem: to switch to the requested tabViewItem.  The General/NSTabView will mark itself as needing display, but the window will not have redrawn yet, so this is our chance to animate the transition!
    [super selectTabViewItem:tabViewItem];

    // Render the finalContentView
    [transition setFinalView:finalContentView];

    [finalContentView setHidden:YES];



Thats fine I guess, but I would rather not be forced to have to time when I grab my screen shots of the views like that. So is there anyway I can grab the focus of the window so all items are active?

Thanks

----

Unfortunately, the terminology barrier here is severe enough that I don't understand what you're trying to do.  I can give you some pointers for google searching though:  "focus" describes something different than what you're after.  One view, if any, has focus in a window, and it typically  draws a focus ring.  A window may be "key", which means that it's the window that receives key events, or "main" which is a little bit weaker than main.  Some other auxiliary window, like a panel, may receive key events first, but actions flowing down the responder chain still pass to the main window if the key window doesn't handle them.

Main and key windows "have active controls".  Active controls are probably what you're interested in.  They're typically drawn with more color and can be manipulated on first click.  Most small controls in Cocoa are active even when they aren't in a main or key window.  Larger controls, like tables, are typically not active in background windows.

You can make a window key with -General/[NSWindow makeKeyAndOrderFront:].

----

Thanks, I will look into the focus ring. I have come to realize however, that my issue might be specific to what I am doing.
