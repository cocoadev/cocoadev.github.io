---
layout: page
title: NSDocument
---

Topics:
[Topic]

The name NSDocument is somewhat misleading. The NSDocument class is intended to be used as a controller (see ModelViewController) for the document data model and in simple cases the controller for the view as well. NSDocument works with both NSDocumentController and NSWindowController to simplify the process of creating a mutli-document application.

also, since Mac OS X 10.1 NSDocument appears to use an FSRef internally to reference a file instead of a file path.

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSDocument.html

See also: NSDocumentDesign (creating new documents)

----

How do you make the document appear modified when you edit a text view (assuming a text view is the main view you use for displaying/editing document data)?

*Well, actually, the undo manager does this for you automatically. However you can look into     updateChangeCount:*

----
What is proper memory management in -makeWindowControllers?

<syntaxhighlight lang="php">
id editor = [[MyWindowController alloc] initWithWindowNibName:@"MyDocument"];
[editor setDocument:self];
[self addWindowController:editor];
[editor showWindow:self];
</syntaxhighlight>

This works fine, but my window controller *never* gets released. If I add a call to *autorelease* when creating the editor, then the controller gets released at the next autorelease pool destruction (ie, immediately). The docs do not seem clear to me, and because they are non-specific, I assume that standard memory management behavior is used. Perhaps there is a bug in my nib? ANY advice is appreciated. --JeremyJurksztowicz

----

As is typical of operations in Cocoa, you need to retain the controller until it's retained by something else.  You should add a call to release following the addWindowController call, which should retain the window controller upon addition.

<syntaxhighlight lang="php">
id editor = [[MyClass alloc] init...];
[self addWindowController: editor];
[editor release];
....
</syntaxhighlight>

-- James Yopp

----
Or alternately, store the window controller in an instance variable, and release it on dealloc.

