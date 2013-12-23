---
layout: page
title: DocDoesNotGetWindowControllerDidLoadNib
---

...automatically, unless you call it explicitly, except under the simplest of circumstances (i.e., no subclassing of NSWindowController)

I have subclassed both NSDocument and NSWindowController. My document does not get a windowControllerDidLoadNib message when a new document is created or an old one is loaded.

What object is supposed to send that message, and when?

----

Hmmm ... *windowController*DidLoadNib?  My guess would be the WINDOW CONTROLLER ... ;-) If you subclassed NSWindowController, that's where your problem is.

----

And yet... and yet...

The document opens just fine. I wanted to make sure I had a reference to another object when the document's window first opens, and I thought the above-named method would be the best place to do it. But never mind, I found another way to do that. The mystery remains...

----

are you overriding a nib-loading method and not calling super?

*There is most definitely a bug in your code. The NSDocument and NSWindowController classes work very closely together. Check *every* method you've overridden in both classes and make absolute certain you are calling the super methods at the appropriate times (some are better in the beginning of your overridden method, some are better at the end - depending on your intentions and the way that particular method functions). The documentation is the only way to determine this for each method.*

----

Now we're gettting somewhere. Here is an inventory of methods in those classes that I have overridden **without** calling super:

In my document subclass:
*
- makeWindowControllers

In my window controller subclass:

*
- awakeFromNib              
*
- windowTitleForDocumentDisplayName:     


The (reputable) example I worked from did not invoke super for its makeWindowControllers or awakeFromNib, and does not have a windowTitle... method

My test is to implement windowControllerDidLoadNib and log some output. The log result does not appear in the run console.
If I set a breakpoint for the log statement, the statement is not reached when the document loads.

There is another window controller in the document, but it is instantiated programmatically when the user activates a particular interface control.

----

Of course, when I call the method explicitly (say, in the main window controller's awakeFromNib), it gets executed, no problem. D'oh!

I just assumed from the method name that it is called automatically by some delegate that is set up by the document architecture.
But then again, I don't know how I would identify which object that might be. I guess I just assumed it would be the window controller.

But now I see that the documentation for NSDocument does not list this method as a delegate method. So I feel like a minor idiot for raising this issue.

Sorry if I have wasted your time.

*Retired. Glad you've solved this :)*

----

It should definitely be called automatically.  Are you calling     [self addWindowController:myWC] for your newly made window controller(s) in your -makeWindowControllers method?  If you make it yourself, you need to call that to assign it to your document.  Another possible problem is that the nib is being loaded before the window controller is assigned to the document.  I've seen code that has something like [self showWindow:nil] in its init method to make their window appear immediately, but this breaks -windowControllerDidLoadNib because the nib is already loaded by the time the window controller is assigned to the document.  -- Bo

----

(OP again) Bo, why do you assert that it should be called automatically? That was, in fact, what I assumed would be done, but is not the case.

POI: Yes, I do add the window controller to my list of WCs for the doc

----

Both the docs and my personal experience tell me that's what happens.  As a test, I just made a pristine 'Cocoa Document-based Application' project and added the line     NSLog(@"windowControllerDidLoadNib: called"); to the -windowControllerDidLoadNib: method.  The text printed out in the run log every time I made a new document with absolutely no other modifications.  In the stack trace, it was being called from the NSWindowController's -_showWindow method, a private AppKit method.  Perhaps if you posted the relevant source code from your subclasses, we could figure out what the problem is better.  Until then, I can only offer guesses. -- Bo

PS  Whoops, nevermind.  I just realized what the problem almost surely is.  The window controller won't call windowControllerDidLoadNib: if the document is not set as the owner of the nib file.  Since the WC is almost always the owner when you use a custom WC subclass, it won't call the method.

----

Even more mysterious, I prepared the nearly-bare bones project and created a window controller **without** specifying the WC as File's Owner for the Document nib file. **Still** no "window controller did load nib", unless an explicit call to the method is made.

As Alice would say, "Curiouser and curiouser". Or would that have been said by the Dormouse?

