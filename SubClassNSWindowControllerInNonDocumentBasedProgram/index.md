---
layout: page
title: SubClassNSWindowControllerInNonDocumentBasedProgram
---

I feel really stupid. I'm trying to create an extremely simple program with just one window (besides the menu). I need to subclass General/NSWindowController. But, as this is my main NIB, File's Owner is General/NSApplication.

I understand that, normally, one would make File's Owner a subclass of General/NSWindowController, but obviously, you also need an General/NSApplication, so that doesn't seem to apply in this case. I've spent hours scouring the net, including Apple's Developer site, and it certainly mentions that an General/NSWindowController can be used alone or in conjunction with a Document-based application, but everyone only talks about Document-based applications. How do I cause an General/NSWindowController to be instantiated without the rest of the Document stuff?

----
Why do you need to subclass General/NSWindowController? You really only need that if you're making a separate nib.

----
I want to provide code for windowDidLoad to customize some of the UI.

----
Use awakeFromNib or applicationDidFinishLaunching: instead, it's basically the same thing (you just want to run code after the nib loads).

----
According to the documentation, awakeFromNib: is called before the object connections are re-established. I'll try applicationDidFinishLaunching: .

----
Actually,     -awakeFromNib (no colon) is invoked *after* all connections in the nib file that was loaded are established.  And I'm almost 100% certain it's documented as such.

In any case, it's not an error to use a subclass of General/NSWindowController to manage a window, and I tend to do so in any applications I write.  The main thing you'll want to do is use a *separate* nib file for the window of your application, and just keep your main menu in     General/MainMenu.nib.  Then you can set your subclass of General/NSWindowController as the File's Owner of your window's nib, instantiate your window controller in your application delegate after the application's launched, and it should all just work.

I use General/NSWindowController subclasses because I like the separation of concerns that they allow by supporting both     -awakeFromNib and     -General/[NSWindowController windowDidLoad].

I use     -awakeFromNib for things that I would normally prefer configure in Interface Builder.  On the other hand, I use an override of     -General/[NSWindowController windowDidLoad] for things like populating controls with initial values (in a non-bindings interface, of course).

For example, I'd set the     doubleAction of an General/NSTableView in my General/NSWindowController subclass's     -awakeFromNib.  I'd also configure a binding for a custom General/NSView subclass there.  But I'd set the initial values of some text fields in a non-bindings interface in my override of     -General/[NSWindowController windowDidLoad].

  -- General/ChrisHanson
