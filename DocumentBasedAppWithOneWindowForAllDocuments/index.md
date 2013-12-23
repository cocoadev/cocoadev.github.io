---
layout: page
title: DocumentBasedAppWithOneWindowForAllDocuments
---

was: DocumentBasedAppWithOneWindowPerDocument  (I thought this was so interesting that it deserved to be titled correctly)

I am developing a Cocoa app that is document-based. the special thing about it is that I want multiple documents to share just one window. If one document is shown the others should be hidden but may be revealed in the same window as the visible document through the items in the 'Window' menu. also the controls state (buttons, ...) should be bound to values stored inside each document.

I have several questions on this:

1. Should I subclass NSWindowController and make it a shared instance (so there's just one instance), so that i can bind it to the document to be shown dynamically via NSWindowController's setDocument?

2. I moved the document window from 'MyDocument.nib' to 'MainMenu.nib' (the application's main NIB file). Is there anything else I should care about because of this, or is it nonsense?

3. Do I need the 'MyDocument.nib' file? or is it possible to move everything from there to the main nib file?

Any ideas would be greatly appreciated. also different approaches to solve this would be welcome.

thanks in advance,
Q

----

If you want to only have one window, you should consider managing document state yourself and not using the NSDocument architecture at all, since you'll be fighting it most of the time.

*I agree completely. I tried to use the document architecture for a project which had only one window and switched views for the different documents. I was finally able to get it to work, but not without trying every different combination of possiblities - if I fixed one thing, another thing would break. If you want more detailed information, I can tell you how I accomplished it without subclassing NSDocumentController. -- RyanBates*

----

I disagree completely. Well, mostly. Let me start by answering the original poster's questions:

1) Yes.

2) No, this is crazy. Create a new nib for your window, and bind it to the NSWindowController normally. See below.

3) Yes you need it. See #2 above, and items #1-3 below.

Ok ... 

I have written a couple of apps that use one window, or one static collection of windows, to display multiple documents. The user can select among active documents using a menu or a control. I used the existing NSDocument system, and found it worked remarkably well -- I only had one problem, and fixing it required subclassing NSDocumentController. And once I had a subclass of NSDocumentController, I was able to do improve support a bit more.

Here's basically what I did:

1) Create an NSWindowController subclass to manage the window state. Make it the File's Owner for the nib file, and hook up the UI. Make sure you override setDocument:; that will be called when the Cocoa system swaps your documents in and out. You can use it to file away the contents of the window into the current document, call super, and then load the contents of the window with the new document.

2) Make sure you will only have one instance of your NSWindowController instance. You can do that using the SingletonPattern FactoryMethod. The factory method will instantiate the instance, cache its pointer, load its nib file, and return its pointer.

3) Create a NSDocument normally. The document merely stores document state -- of the document UI is provided by a standard NSWindowController subclass. That means instead of returning windowNibName, you implement makeWindowControllers. In makeWindowControllers you will call your NSWindowController subclass factory method and add the window controller to the document. Behind the scenes, AppKit will remove the window controller's current document association for you -- you get that for free.

4) Provide a control somewhere for switching between the different documents. You could trick the system into putting the documents in the Window menu where windows go, but I don't recommend doing that. Instead, use a different menu; or use some other control like a popup, a list, or a tab view. When your UI is selected, simply get the new document, the shared NSWindowController subclass instance, and add the window controller to the document.

5) By this time, your document should be working pretty well. You can create and open documents. As you switch between them you'll note the "document change dot" in the close box will follow your document selection. Etc. You have two bugs here, though, a small one and a big one. The small one is handling when the window closes. You want to provide a way for the user to close the current document w/o actually closing the window. Generally I do that by overriding the NSDocument subclass' -close method, remove the document from the window controller, and  then close the document. There are a few other small edge cases here but you'll make quick work of it. 

The larger bug is if you try to quit when you have more than one dirty document, and you Quit, and then review unsaved files, the NSDocument system will try to display two sheets on the same window at once. This is forbidden in Cocoa-land and is a quick way to mess up your app. The fix for that is as follows:

6) In your MainMenu.nib make a subclass of NSDocumentController, and instantiate it. Now whenever you say [NSDocumentController sharedDocumentController] you'll get your version instead of the default one.

7) override NSDocumentController's closeAllDocumentsWithDelegate:didCloseAllSelector:contextInfo: method. You want to drive the cycle yourself, one document at a time. For each document, call canCloseDocumentWithDelegate:shouldCloseSelector:contextInfo: and register a callback. In your callback DO NOT ask the next document to close; instead get the shared window and register for the NSWindowDidEndSheetNotification notification. When *that* notification fires, ask the next document to close. 

I know it sounds a little complicated, but it's not too bad. I haven't checked in a while, but maybe this bug has been fixed by AppKit already. 

8) After that -- you should be good to go. Here's what you didn't write yourself:


* You didn't write your own document architecture. You got that for free.
* You didn't have to patch into the NSResponder chain to handle events properly -- you got all that for free.
* You didn't have to worry about how undo works -- you got all that for free.
* You didn't have to get Open Recent working. Yep -- free.
* Printing. YMMV, but you don't have to do anything special. NSDocument gives you some of that for free.
* Reviewing open documents at quit-time. OK -- this wasn't free, but it wasn't too expensive.


Enjoy, and good luck. Be sure to post additional questions, and sample code when you get this working.

-- MikeTrent

*Ahha, that's the step I was missing (subclassing NSDocumentController). Thanks for posting this. I should have tried that before. :) -- RyanBates*

----

Thank you very much, Ryan and Mike, for outlining your solutions. I will follow Mike's advice. I'll get back here and tell you about my experiences.

Thanks again,
Q

----

I understand pretty much all of it, except for the portion of step one where it says to make the window controller file's owner of "the nib file". Which nib file? The MyDocument nib or MainMenu nib, or create one especially for our special document window?

----

Here's another option:

Use multiple windows like normal. But hide all of them except for the active one.

When you need to switch documents, just hide the current active window and bring forward the other one. Make sure the window frames match up, and the user won't even know.

This way, you're not fighting the document architecture, each document can control its own window with its own window state and settings (title, document changed status, undo manager, etc).

*But windows are expensive.. Mail even warns you if you try to open more than 10 windows, and it should.  It takes a long time to get those windows going!*

----

Are they really expensive? Considering Apple's liberal use of them (Dock icons, field editors, tool tips, menus, status items, drawers), I would expect them to be pretty cheap.

*Here we're duplicating not just a window, but all of its contents (views, controls, etc.).  Even an NSView is considered fairly expensive.  Hence the distinction between views and cells, and all NSTextField s using the same field editor, etc.*

----

CPU cycles and memory are cheap. The expensive resource is your time and effort.
*Bullshit. End users can see exactly what resources your app is taking up by running Activity Monitor. If your app is less efficient then your competitor's, guess who loses.*

Sorry, but I have to interject - most users won't even know to do that. Those who do will balance performance against features and a nice interface. It's not all black and white.

*It's more black and white than you think. Those of us who are capable of performance measurement will get the word out. Mind you, I'm not saying that the all-in-one-window thing is preferred for performance reasons (or other reasons), but CPU cycles and memory are really expensive, and if you're wasteful with them without anything to show for it, you'll have no one but yourself to blame (well, you could also blame nameless 'pirates' and Apple if you're Black & Bleue Software).*

Yeah... There's no explanation for why no one's buying a list of OS9 error codes other than declining # of Mac users and piracy....

----

I just opened 20 windows in Mail and didn't get any warnings. 

*No warning if you do it sequentially.  Highlight 20 messages and press return.*

Well, sure, but that's not because it's so expensive.  It's because when you highlight 20 messages and press return, it is very very likely that you did it accidentally, and that you don't want to litter your screen with windows that there is no easy way to close again.  (Command-shift-W will close everything, including all the windows you had open before you made the mistake.)  Don't point to this as anything other than what it is: a convenience to protect you from doing something that is almost never what you actually want to do.

*
As a matter of fact, I do this quite often.  That's why I knew about the warning.

Here's the situation, in case you're curious:  I use Mail.app with the preview pane closed, as I prefer to read messages in their own windows.  If I haven't been following a mailing list, I'll go to the list and open all the messages I haven't read.  Windows open, I close them one by one as I read the messages.  Works like a charm, except it takes maybe 10 seconds for Mail to become responsive after I ask for all the windows.
*

----
Is there a way to adapt the above procedure to have multiple windows of multiple documents each?   For example, something like Text Wrangler or Mail.   You can open up new viewers/frames/windows, and each viewer lets you browse a bunch of documents.   I'm wanting to write something like that, but it isn't clear to me how hard this is with the NSDocument architecture... -kolmogorov 

----
Did anybody get the procedure above to work? I get to the point where the NSDocument method -close is overridden, but I don't really know how to remove a document from a window controller.  The next problem is that -canCloseDocumentWithDelegate:shouldCloseSelector:contextInfo is not called when I subclass NSDocumentController and instantiate it in my MainMenu.nib. I would be also interested how it is possible to create a multi-window, multi-document app like text wrangler. Can anybody help here?

----
Here's how I have implemented my multiple-window multiple-document app so far:
 
1) Subclassed NSWindowController and overrode windowWillClose so that it sets self.document to nil and calls the document's removeWindowController method if the window controller is in the documents list of window controllers.  This prevents the document from being closed when the window closes.

2) Implemented a mechanism for switching between windows.  I have a popup window with a list of open documents, when the popup is changed I simply set self.document to the appropriate document and my window adjusts its display automatically.

3) Implemented makeWindowControllers in my NSDocument subclass to create a new instance of my window controller and call addWindowController with the newly created controller.

4) Implemented application delegate method applicationShouldTerminateAfterLastWindowClosed to return NO since I likely still have open documents when the app quits.

5) From application delegate method applicationShouldTerminate, I call reviewUnsavedDocumentsWithAlertTitle and either cancel or allow the terminate depending on the results.

So now I can open new documents with the file menu as normal and they get a new window.  I can then switch between documents in any open window using my popup.   One issue is that you get an unsaved document alert when you close the window, even though you really don't need one since you're not actually going to close the document.  Another thing is that I still have to implment a mechanism for closing open documents without having to quit the app.  This is really just a matter of figuring out a good UI as it should be easy enough to do the document close.

Update: The logical fix for the alert issue would be to disconnect the document from the window controller before the alert has a chance to be displayed.  However, it appears that the document controller is told to handle the window close event before the window controller (which is messed up since the window controller could cancel the close event if it wanted to, potentially leading to a situation where the user is asked to save a document because its window is closing and then the window doesn't actually close).  From what I can tell this means you will have to subclass NSDocumentController to get the alerts working properly as was mentioned above.  Note that the same issue occurs with the app terminate message - the document controller attempts to save all documents before the app delegate is notified that a terminate is even occurring.

