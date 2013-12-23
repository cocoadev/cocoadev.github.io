---
layout: page
title: MakingNibsTalkToEachOther
---

This page is linked *from* the page CommonProblem

I want to send messages from an object in an auxiliary nib file to another object in my document's object tree.
What I want to do is to send an action from a panel in a nib file called     Properties.nib to the NSDocument in     MyDocument.nib.

----

Every nib file has an owner (FilesOwner). A typical     NSWindowController nib file's owner is a     NSWindowController object. What you should do is subclass      NSWindowController, make the Properties.nib's file owner an instance of your subclass by selecting the file's owner in InterfaceBuilder, pressing Command-5 and selecting your subclass in the list. Make sure every user action in Properties.nib objects sends its message to the nib file's owner, i.e. to your     NSWindowController subclass object. Then edit the action methods definitions in Xcode or ProjectBuilder. You can always access the appropriate     NSDocument subclass from its     NSWindowController by sending [self document].

----

One way I've been able to solve this problem of "modular nibs" is by making several nibs share an owner.  This is much easier if you are initializing your own nib by hand.  This sample should help demonstrate the concept.  I'm not sure if this is evil or not, but it seems to work well enough.

    
- (void) awakeFromNib
{
	// assume you have an alloc'd and retained ruleEditorController as an instance variable
	[NSBundle loadNibNamed:@"PHMenu" owner:ruleEditorController];
	[NSBundle loadNibNamed:@"PHRuleEditorController" owner:ruleEditorController];
}


-- MikeSolomon

----

Nice tip, but I'm curious, is there an advantage to having two separate nib files if they are going to have the same owner? Why not place it all in one nib file? I also do not see how this will solve the initial problem because he probably does not want a separate panel to be created for every document (which would have to be the case because a nib file can have only one owner). 

Also, a note to any cocoa beginners, if the nib file is complex, be careful not to overload the owner with everything that relates to that nib. If you do, before you know it, the owner will be over 3,000 lines and hard to manage. Try to stick with the single responsibility rule and pass on some of the work to smaller controller objects.

----
There are two advantages to having separate nibs: organization and usage; some people like having separate windows in separate nibs. You can then choose whether or not to load a nib **at all** during your application's execution. Example: If your application has a complicated preferences panel, you might want to move that into a separate nib file, which is loaded on command instead when the application is started. That way, if the user never uses the preferences panel, your application won't require the extra memory space that had previously been needed.

For your application, you should probably put some sort of in-between class in your Properties.nib file. This class would call     [[NSDocumentController sharedDocumentController] currentDocument] in order to get your custom document subclass, then send the document a message. This is how I would do it without using FirstResponder. On the other hand, the First Responder method is probably cleaner, and would allow you (theoretically) to implement a delegate that would catch the message, if the need ever arose. If I'm making an error here in my views, someone more wise than me please tell me (that's probably 92% of the site's users) --JediKnil

I had the same problem when working through chapter 13 in Hillegass 3rd edition.  He had you create a subsidiary .nib for a preferences panel with a color well to change the background color of a tableView in the main document .nib.  The way he programmed it, you had to quit the program and restart it before you saw the change.  I used the above suggestion, and it worked out great.  If I have several documents open, I can colorize them seperately.  Here is my (relevant) code.
             MyDocument * myDoc =
			[[NSDocumentController sharedDocumentController] currentDocument];
	[myDoc setColor: color];            
        //  setColor is a method in the MyDocument class
        - (void) setColor: (NSColor *) color    {	[tableView setBackgroundColor: color];  }                 (Then there's chapter 14 using notifications.)  -- CBRader


*putting lesser-used stuff in separate nibs will also speed up your app's startup time.*

----

So, say I had two nibs-- a document window, and an Inspector window. The document's window controller has an NSArrayController in its nib, which I want to bind the widgets in the Inspector window to. Although I can retrieve a pointer to the document and its window controller when I load the Inspector window, I'm having a very difficult time binding my Inspector to the array controller-- I've tried just about everything I can think of except manually creating the bindings for each GUI element, but nothing seems to work! Creating a binding from the inspector directly to the data works fine, but of course the inspector is not linked to the array controller's selection.

----

What do I need to do to create a link between my NSWindowController object or even my NSDocument object and another object instantiated in its MainMenu.nib file?

What I want to do is use a new defaults controller class instantiated in the MainMenu.nib to control a prefs window that is also instantiated in the MainMenu.nib file.  At this point, though, I want an outlet in my NSWindowController or my NSDocument (not sure which way I'll go, yet) to refer to a value in one of the keys in the prefs controller so that I can then base a decision on that key value.

I would prefer to keep the prefs window out of the document's nib (though this would be easy to accomplish if the prefs window and the prefs controller were part of the document's nib).

----

You can set the application's delegate to some object in your main nib, make connections to this one 
and then ask [NSApp delegate] or [[NSApplication sharedApplication] delegate] (slightly cleaner IMHO)
from objects in your document nib.

CocoaBindings makes it much easier to manage user defaults

----

I'm looking for a more general purpose solution--say if there were two objects in the M**'ainMenu.nib that my document (or an object in my document or its nib or some NSWindowController in my app) needed to send messages to.

Say I have my MainMenu.nib that typically only has my main menu in it.  But let's say I want to do something when a particular menu item is chosen.  I would assume (and remember, I'm new to this stuff) that I would connect the menu item to an action in some object in MainMenu.nib.

But what if this menu item is supposed to affect the document somehow--say cause an action to run in the document object, or execute some method in an object that was created in the document.  How would I do this?  How do I get the document to request info from some particular object in the MainMenu.nib or get this controlling object in the MainMenu.nib to somehow affect the document by sending messages to the document or one of the document's objects?

I clearly can't connect outlets in objects residing in one nib to objects in another nib, right?  But there must be some way for objects in one nib to talk to objects in another, right?

*see also MenusAndDocuments for other ways to manage the ResponderChain in document-based applications*

I just did some reading on the responder chain and I managed to get a menu item to execute an action in the document itself.

BUT, how do I get a menu item to execute a method in another object (some view-controller for instance) in the document's nib without having to relay it through a seperate action in the document and without that object having to become the document's delegate?  (I think I need to somehow insert this controller object into the responder chain.)

----

Can this object be a window delegate? That will get it in the responder chain. Otherwise see NSResponder's     setNextResponder:(NSResponder*)aResponder method.

If this is a custom view, a better design might be to insert <code>-(BOOL)acceptsFirstResponder{return YES;}</code> in your NSView subclass, and pass messages to your controller from there.

----

That's it.  I'll make it the window's delegate.

----

Someone above noted that CocoaBindings make defaults easier, but what about using Bindings to connect something in one nib to something in another nib? For instance, an inspector in a separate nib loaded by the application inspecting something in the current document?

----

If the object you're looking for doesn't exist in a nib, then you can't get to it through Bindings or otherwise.  Bindings are just sugar; the main problem of communicating between nibs is that they are freeze-dried packages of objects.  If you want objects stored in two different nibs to be able to communicate, there needs to be an object that exists independently of the nibs that both nibs can refer to, i.e. File's Owner.  If the same object is the File's Owner of both nibs (say, for example, and NSWindowController subclass) and File's Owner has outlets for whatever data you need to communicate between the two nibs (such as an NSDocument reference) then yes, Bindings makes it easier to do what Bindings are intended to make easier.

That's really not saying much, which is the point.  The concept of Bindings is completely separate from the concept of using multiple nibs.  Think of it this way: if you put a user defaults controller in a nib, intentionally or automatically by referencing it through Bindings, that object doesn't actually store the defaults data, it merely acts as a proxy to the shared defaults, which exist completely outside of your nibs (and your application for that matter).  Using File's Owner in the same manner (a proxy object for accessing other objects in the nib) will get you what you want.

----

Asked above: "Nice tip, but I'm curious, is there an advantage to having two separate nib files if they are going to have the same owner? Why not place it all in one nib file?" One reason not mentioned yet for having things in multiple NIBs is that with some things, you may want to use the NIB as a way of creating multiple copies of whatever's in them. NIBs are freeze dried objects of a sort, and loading and potentially re-loading a NIB is a way to replicate what you want more than one of - consider documents for example in a multi-document application. 

http://www.om-onny.com/ | http://jasaseomurah.om-onny.com/ | http://pasangiklanbaris.om-onny.com/ | http://www.om-onny.com/2011/11/zodiak-hari-ini.html | | http://www.om-onny.com/2011/04/buka-aura-dan-cakra-tenaga-dalam.html | http://www.om-onny.com/2011/10/cara-membaca-pikiran-orang-lain.html | http://www.om-onny.com/2012/01/cara-membuka-indera-keenam.html

