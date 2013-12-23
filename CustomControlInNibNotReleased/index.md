---
layout: page
title: CustomControlInNibNotReleased
---

I checked my application with ObjectAlloc.app and discovered that not all my custom objects in a nib file are released.
I fixed this by adding a

    
[theCustomObject autorelease];

in the dealloc method of my fileOwner class.

This is working for simple custom NSViews but I'm unable to release things like a custom NSColorWell.
For testing purposes I simply sub classed NSColorWell without adding any method.

Now, when I autorelease this custom class my application crashes, if I don't autorelease each time I load and release the nib file an object is created and remains allocated.
Why is this working with simple subclasses from NSView and not with more complex objects?

Or... how should I handle this?

FYI:
My nib file is loaded with the code below.
myFileOwner is the class set in InterfaceBuilder.
The nib file consists of a file's owner, a first responder, and a window.
    
id fileOwner=myFileOwner alloc] init];
 [[[NSBundle loadNibNamed:@"bundleName" owner:fileOwner]


----

First of all, how do you "release a nib file". That's one I've never come across before.

If you are getting objects instantiated in a nib file, why do you need to load that auxiliary nib file more than once? Me, simpleton that I am, just check to see if the thing has been loaded previously before attempting to load it, and, if so, skip it the second time around. Yikes!

if ( myNibbyLittleThing == nil ) {
[ NSBundle loadNibNamed bla bla bla ]
}

Of course if you over-release an object, your app will crash. At what point is your dealloc method not "working"? When you "release" the nib? (Though I have no clue what that could mean!) If you need copies of the object in the nib, maybe you should be using a document-based scheme. Hm? On the other hand, if you are worried that not all your deallocs are happening when you quit the app, this is a separate matter.
----
The nib file is a dialog, sheet, which can be used by more than one document window at the same time.

So, yes I guess I'm looking for a way to release a nib file once I'm done with it until I need it again or need another one.
There are many more of these dialogs and keeping them all around is overkill.
Until now, I was assuming that once I release the file owner, the nib would be released also. This is obviously not the case.
Question remains... How to fully release a nib file. Do I really have to release (or autorelease) each and every item?

----

I thought releasing the FilesOwner was the way to do that. You must be doing something else funky in there. Yes, you might have to do it all programmatically and without the use of a nib file. Why aren't you using the     [ NSApp beginSheet] stuff?

Perhaps I am totally out to lunch here, but this seems to me rather a perversion of the Cocoa frameworks, and what you wish to do may well be impossible. If only I could figure out what you are actually trying to do, I could be more positive (both literally and figuratively).

I can only ask you why you are trying to hack (up) the concept of a dialog / sheet. Can you not get by with inserting custom views into a standard sheet implementation?  There is an appropriate way to do this programmatically. In other words, put a custom view into the nib that is owned by the object that issues the (standard) sheet and programmatically set that view in the sheet. Read some documentation on the method     setAccessoryView: and see if it does not offer you a way out of this yucky situation. That last is not a very technical term.

This just seems completely alien to me. You seem to be using a mental model of a dialog / sheet that could have originated in MFC or wxWidgets or that Qtips thing, whatever it's called.
----
Ok, for a moment don't think about custom controls any more.
Say you have a document based application.

It is not abnormal to be able to open a sheet in a document.
Suppose the sheet is a simple dialog and once that dialog is filled out by the user the contents are used to write text in the document.

Nothing is preventing, or should prevent, the user to open the same sheet on two documents at the same time.
For every document, you need a unique copy with its own values and strings for the objects.
Is this hacking things? I guess not it is only not a common thing to do but it should be possible.

What I want to do is rather simple, load a nib and once I'm done with it dispose it, making sure memory is released and not wait until the application quits.

Just imagine very complex sheets with a lot of objects and perhaps images, this uses quite a lot of memory.

----

That is not so bad. I have done it myself in a document-based app: Open a sheet on the master interface of a document view in order to present the detail part of a master-detail interface. Never had a problem with that. You have not said why [ NSApp beginSheet ] is not adequate for your needs, except to say you want the sheet to have lots of doodads on it. Sounds like an interface design problem. Design a panel, with lots of doodads if you like, and present it as a sheet. Add the doodads programmatically if you must. The panel should be part of your document nib.

I sure did not need to load a nib in order to present the detail interface. At the risk of antagonizing you, I feel you are being somewhat obstinate in insisting that your way is the only way to do what you want, that is, without giving more details about your interface. Sheets with accessory views are not out of the question, but I can do no more to guide you.

----
*
The OP asked a relatively simple question: "Do I really need to release each and every object in the nib?"

As I understand it, the answer is that your nib file's owner generally needs to release all the top-level objects. Subviews in a view, for example, should be released when you release the view that contains them, so you should only have to release the top-level view and not all its subviews unless you've retained references to them elsewhere. I say 'generally' because NSWindowController releases things for you. It sounds like you (like most of us, probably) may have become accustomed to that behavior and aren't used to releasing things yourself. If you haven't already, check out the "What Happens When a Nib File is Loaded" page in the Cocoa docs.

To solve the problem, make sure that each top-level object is being released. Also, look for places where you might be retaining references to some of the objects in the nib, and make sure that those references are released appropriately.

Also, why are you using -autorelease rather than -release in your -dealloc method? If your file's owner is being deallocated, then it should be able to safely release its references. You usually only need -autorelease when you've allocated something that you're handing off an object that you've allocated to another object which may or may not want to keep it around. Using -release will decrement the retain count and potentially deallocate the object immediately, whereas -autorelease will decrement the retain count at some point in the future, so the object may not be deallocated for a while. It may be that your use of -autorelease is causing these problem objects to appear to stick around, when in fact they just haven't be deallocated yet.

It might be helpful if you can post your fileOwner's class declaration and -dealloc method. -CS
*

----

As the one who responded initially to the OP, I say bravo! And to which I can only add that the objects associated with the document, including those in associated sheets, should be released when the document is closed. If objects are added to those sheets programmatically, there is probably an appropriate place to release them, as when the sheet is closed.

This is quite simple if the panel on which the sheet is based is a top level object in the document nib. The nib's FilesOwner has an outlet to the panel, and can, if necessary, create objects for it programmatically, can it not? And release them in the dealloc method or when the sheet is closed?

To amplify the "simple" question initially posed by the OP, note that it specifies that the *nib* for this sheet is explicitly loaded, ostensibly to present the sheet. I am only saying that this is not the way that I would choose to try to do this. I think I would only use NSBundle to load a nib on a one time basis in a non-document-based app. But I suppose there are ways to get rid of the objects created in this manner, for example by having outlets to EACH of them from that nib's FilesOwner, and releasing them individually at an appropriate time (and needing to check then if they are not nil).

----
Problem solved!
Indeed, one of the top-level objects wasn't released.

Once I do that, all objects are released and I have of course no more leaks.
And yes, I do use [ NSApp beginSheet ], no I don't think that my way is the only way but I want to understand what is happening in this case and why.

Thanks for your replies, my questions are answered.
I have a much better view on this whole nib and memory stuff now.
Thanks again!

----

HowToDuplicateAViewFromTheNib may contain an interesting twist on this stuff

