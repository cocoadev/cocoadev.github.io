---
layout: page
title: WhenToOverrideMakeWindowControllers
---

Hi, been fighting with this one for several hours.  I have an General/NSDocument which has multiple nibs.  Each nib has an associated General/NSWindowController. I want to show one of these nibs (windows) when the document loads, but not the others.

I've overridden     makeWindowControllers in the General/NSDocument, initialized all of my General/NSWindowControllers, and called     addWindowController on each.  I'm thinking this is not the way to go about it, since every General/NSWindowController subclass that overrides     windowNibName shows itself when I create a new document or load one.  I just want ONE of them to show.  Ticking "visible at launch" on the windows doesn't seem to make any difference.

Should I be overriding     makeWindowControllers here?  Would it make more sense to create one nib with multiple windows (that would get really messy I'm afraid)?  Instead, should I have a menu item (for example) initialize an alternate General/NSWindowController, then add it to my General/NSDocument w/     addWindowController?  Or is there some way to load a nib without actually displaying it?

Thanks!

----

As far as I can tell, you're doing everything correctly, if you've been turning off "visible at launch" in Interface Builder.

You're **only** creating the instances of your General/NSWindowController subclasses in your override of     -General/[NSDocument makeWindowControllers], right?  And you're not also trying to use an General/NSDocument nib file, just the ones owned by your General/NSWindowController subclasses, right?  If so, the only thing I can suggest is to make sure you're not invoking     -General/[NSWindowController showWindow:] or     -General/[NSWindow makeKeyAndOrderFront:] until you actually want your windows to be displayed.

If you've made sure of all of that, it would probably help to post your code.

----

Apparently the shared General/NSDocumentController was calling     -General/[NSDocument showWindows:] as the document was opened.  I overwrote that method in my General/NSDocument subclass to open *only* the window I wanted.
