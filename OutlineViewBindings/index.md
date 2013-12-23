---
layout: page
title: OutlineViewBindings
---



Does anyone have information about using Cocoa Bindings with an NSOutlineView?

----
As far as I know, it's not supported yet.

----

Where *yet* should not indicate that it will be. One Apple employee said we should send a feature-request if we want it. Personally though, I have a hard time imagining how it should actually be done.

Maybe if CFTree was given a Cocoa front-end we could get an NSTreeController, but (going into rant-mode), this MVC starts to resemble a story about the WiseMenOfGotham: We need to split model and view because we oh-so-often need to re-use the model in another context or the view, so we write glue-code that far surpasses the length of what the all-in-one application would have occupied, and that starts to suck, so we start to look into re-usable controller-code, and we get one that ties an NSTableView to an NSArray (although not handling drag'n'drop (nor drag-sort) and stuff), and we may get a similar one which ties an NSOutlineView to an NSTree -- so we need to instantiate 3 objects and connect these every time we actually just need one, all in the name of modularity, re-usability or whatever.

That said, I do see some advantages in MVC and I am also in favor of modular code, but seriously, when the rule rather than the exception is to use an NSTableView with an NSArray or NSOutlineView with a CFTree, would it not make more sense to only require extra code if we want something other than this?

----

I think MVC makes a lot of sense. You seem to be arguing to put everything into one object, so the view which is displaying your document's contents will also do the loading and saving of the document.  If you then decide that your program needs two views to show the same document you are now stuck with having to pull the data storage needs out of the view code and into another object,  When you start out with a clear distinction between data storage needs (model) and data display needs (view), in the end you end up with a much nicer architecture.  And when your programs get to be dozens or hundreds of classes, an extra controller class here or there is no big deal. 

----

No, I do not argue to put everything in one object in every situation. There are other ways to modelize code and keep separation of concerns without having to write three objects when only one is needed.

You say that when you have hundreds of objects, an extra controller doesn't matter. Here I strongly disagree. First of all, we are talking about either huge controllers or dozen of them, and often these replicate work (or at least "abstract knowledge"), so a change in the view and/or model will propagate to the controller-layer (and thus require changes to be made in two places instead of one).  So the complexity is sometimes higher, not lower, when writing layered software (also because the layer-communication is often not 'thin').

Not that this is really my reasoning, but it's interesting nonetheless, from http://weblogs.asp.net/chris_pratley/archive/2004/04/27/120944.aspx

*[...] Just Systems decided they needed to redo their whole application as "component software" (a fad at the time) [...] the rewrite took too long, and in desperation JustSystems put out a buggy, slow (over 30sec to boot [...]) Ichitaro that actually had less [sic] features than the product it replaced. It also required more memory [...]*.

i.e. "component based" or "modular software" is a trend, and no-one would argue against it, because it is common sense that we want stuff isolated in modules, which can then be replaced. The problem is that on paper, the overhead of module-communication is not visible, and complexity of code is often underestimated, i.e. an application at 4,000 lines is rather easy to maintain and large parts can be re-written in little time, whereas something around the 30,000 lines of code is much harder to maintain (this of course depends a lot on those who did the 4,000 lines version, if it's spaghetti code, the point doesn't hold).

Unfortunately the tools to keep down the size of programs are not always available, but in the recent years a lot of focus has IMHO been given to it in form of Array Programming and Aspect Oriented Programming, which can all be used to keep down the size of the program enormously.  Also, recent mainstream adoption of languages like Ruby, which combine a lot of neat ideas from FunctionalProgramming, also help.

Another problem is that the re-usability in object oriented modular software is IMO highly overrated, when using flat hierarchies it helps, but then we are really not doing OOP but more just DataAbstraction, and we would often be better off using GenericProgramming and/or a language which support DynamicTyping.

----

To me, this discussion boils down to the basics of encapsulation. There are both wrong ways and right ways to do it. See EncapsulationBasics. --RyanBates

----

Doesn't it suck when people hijack a conversation?

FYI, NSOutlineView bindings are coming in Tiger, but no idea what they'll look like yet. Wish I did know so I could fake it for the time being. I like the tree approach, maybe a hiearchy of arrays of arrays...

----

While we generally do not want NDA stuff in this Wiki in fear of Apple's legal department, it would be great of someone could write a hypothetical post about how one could go about providing bindings for outline views! :)

----

I was able to do it with a browser.
The trick is to add a parallel and bindings-compliant selection tracker to the hierarchy of the UI in the datasource or delegate, then in your data source or delegate (browser uses a delegate as its data source), you keep a reference to the most recently-selected object... that's object not cell.  And then you create a controller object that has your delegate/datasource connected to its content outlet.  now all you need to do is figure out the specifics of what you need to bind, and how to update the UI of the offending non-bindings-compliant UI.  As I said, I was able to do all of this with a browser.  it took, 3 classes... one to track my selection with references to the objects, a browser delegate, and my generic object.  didn't have to override the browser at all.
Best part.... no NDA!

----

Is there any chance the previous author could expand on this at all?  It doesn't seem lilke it would actually save much work :/  Care to enlighten us? ;)  If it does save work I'd love to know how to do this.

JKP

----

I'm the author.  
two factoids:
1. it saved work "downstream".  made my browser compatible with All of the interface elements I had bound to it.

2. XCode NOW has a Hierarchical Object Controller.  making all of my work obsolete, and supporting both browsers, and outlineViews.
this exists in Tiger, and was quietly added to IB.  I haven't explored it yet, but I intend to.  in the IB pane with little green Boxes, it looks like little green Boxes with complex Plumbing added to them.

somebody give it a whirl. let us know how it works!

----

It does indeed exist in Tiger, but enlightenment for those of us targeting 10.3 systems would be a bonus.  Many thanks

JKP

----
JKP, I wound up making insane changes to my browser to get it to work with drag n drop Plus bindings, I'm trying to find the relevant bits, and familiarize myself with them as i do... I'll try to sketch you a plan now.

I needed to organize a particular kind of object in the browser, so I made OBJClass and OBJClassController.
OBJClass is the object we wanted to see in the browser, and the controller is essentially a data source on steroids, with an array of OBJClass objects (what populates the browser), and an array that is populated by the Path of the selection (each entry is a child of the previous entry). I do this to support dynamic movement through the hierarchy, and to make my selection available to my app as well as bindings.

I subclassed the OBJClassController, and made a BrowserController.  Now my browser controller IS the data source as well as the Browser delegate.  The code is Neat and Tidy.
this makes responding to "numberOfRowsInColumn:" and the other delegate methods easy.   BUT... it also allows me to ignore the fact that the browser itself is not bindings compliant (because I keep track of the selection and tell the browser what to do.  my BrowserController can be completely bindings compliant, and since IT keeps track of the current selection instead of allowing the browser to do it, then we can do things like bind to its current selection.

But you have to make an instance of it in IB...   which you can!  subClass NSObject in IB, give it a name, control click on the class and IB will let you Instantiate a controller class! don't forget to add it as the browser's delegate!

now you make an NSObjectController object (green box with circular arrows on it in IB) and connect its "content" outlet to the BrowserController.

you have to setup your Key paths, but thats it! Your controller is Now bindings compliant, with it you can load settings from the object selected in the browser auto-magically in multiple UI elements. and they update auto-magically too!

I will follow up later with some code examples, but its not terribly complex.  Ok it IS complex, but only in comparison to what you have to do to get other UI elements to work with bindings.

----

to the author of the above....im still very much interested in what you have described above.  I have no familarised myself with writing IB palettes and also with exposing bindings on the objects i palettise, so i was thinking that maybe it would now be possible to subclass NSOutlineView / NSBrowserView and make bindings compliant versions....

I currently have a tree class that is a bit like CFTree - I was hoping to use this as the base currency in my attempt....id love to be able to offer a BSD licenced alternative to NSTreeController for 10.3 users...

JKP

