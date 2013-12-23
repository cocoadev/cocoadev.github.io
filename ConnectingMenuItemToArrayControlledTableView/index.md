---
layout: page
title: ConnectingMenuItemToArrayControlledTableView
---


[Newbie first-time post alert! Feel free to inform of etiquette violations]

I am new to Cocoa/Obj-C, having just finished the excellent Hillegass book, and am now mucking around with some code, trying to get a better handle on things. In my current (NSDocument-based) app, I have a document nib, with an NSTableView that is bound to an NSArrayController for its content. There is also a button, "Remove", that is targeted at the "remove:" action of the NSArrayController. All that is working fine - I can add and remove elements, sort, etc. But this remove button is ugly, and not terribly useful - the standard Edit menu already comes with a handy "Delete" entry, so I would like to use that instead. Of course, I can't connect that item (in MainMenu.nib) to my NSArrayController (in MyDocument.nib), so I connect it to the First Responder. Normally, the action for the delete menu is "delete:", but NSArrayController has a "remove:", so I added a new action to the First Responder in MainMenu.nib for "remove:" and connect the delete menu item to that (this may be a mistake - is it a bad idea to change a well-known items action? Will it come back to bite me?). Next, I made the NSArrayController the delegate of the NSTableView. My understanding is, something like this should happen:

1. When the menu is opened, the menu system should ask the first responder (the NSTableView) if it supports the "remove:" action.
2. If it doesn't (which it is true - it doesn't), it will ask the delegate of the first responder if it supports "remove:".
3. If it does (which it should - NSArrayController lists "remove:" in IB, and it worked for the button previously), the menu should be enabled.

Problem is, it isn't enabled. Does anyone see what might be wrong? Is it possible the NSTableView isn't really the first responder? Perhaps the NSScrollView is really the responder here? If that is the case, how do I solve this? Or is it wrong to have the NSArrayController be the delegate? Should I make my document be the delegate instead? 

----

In all likelihood, your array controller is connected to your main document controller. What you should do is add a method called "remove:" to your document controller, which in turn calls the remove: method on your array controller. Your array controller is not in the responder chain, so your menu never validates.

----

It is indeed connected, and I could do it that way. But I may have multiple NSTableViews in the window (I don't now, but I may eventually). Now, I know I can figure out who the first responder is and figure out which NSArrayController needs to get the message, but that seems like a lot of work, considering I can do the button with no additional code. As for the responder chain, it is true the array controller is not in the chain, but the NSTableView should be, and since I have set the array controller to be the delegate of the table view, shouldn't this work?

----
I say this without malice: You'd better go back to the basics and revisit your book. You seem to be confusing quite a few details and I'm really not sure where to begin. I may be misunderstanding your statements the way you're presenting them, but several things must be considered. First, you seem to be ignoring the MVC (model-view-controller) paradigm principles. You should definitely re-read any materials you have on that concept. It's of vital importance for any Cocoa developer. Second, why are you making your array controller the table view's delegate? Have you subclassed NSArrayController and given it the appropriate methods (which the stock NSArrayController does not have)? Or do you mean you made it your data source? If the latter, the only reason most people do this is to add drag-n-drop capabilities. For now (for your initial learning period), either use Bindings or the data source methods, do not mix both as you'll only confuse the hell out of yourself. :-) Finally, you should read **very carefully** the Apple documentation regarding FirstResponder. You've completely misunderstood the concept and that's one of your biggest problems here, IMO.

----

Well, let me start off by saying it is *way* to early for me to be going back to basics - I'm still smack-dab in the middle of basics! This is my second week with Cocoa, the first of which was spent working through Hillegass, so bear with me. Let me answer the questions you raised, as it is entirely possible (nay, probable!) that I am doing stupid things here. Now I just need to figure out the stupid bits.

----

Whoa! You covered the book in one week?? Methinks you should slow down just a bit... obviously, there's a difference between reading and understanding the material. It takes more than a week to understand Cocoa, especially if you're new to its background essentials or C-based languages. Give your brain time to absorb information before you try to dive into stuff like this. It's hard to have two tableviews co-exist without some additional tricks, which aren't covered in depth in the book. You should concentrate on making a single list of items work first, then worry about the more advanced stuff. Otherwise, you end up over your head like I was when I started. Cocoa needs at least six months to get the hang of it.

----

A week seemed about right - that's how long the related course is. In any case, it was a good enough book that taking longer would have driven me nuts - I kept wanting to find out what came next. Of course, I am still in the learning stages, but I wanted to start putting into practice what I just got from the book. It's all well-and-good to follow a teachers directions, but it's not quite the same as looking at that blank sheet of paper. I don't plan to put more than one table view on this window, but I would like to understand the correct way of doing this task, so I don't shoot myself in the foot in the future. So, any help out there? What would a Cocoa expert consider the "right" way to hook up a "Delete" menu item to an NSTableView bound to an NSArrayController? Do I subclass the table view? Or do I add a routine to my document to figure out if the table view is first responder and has a selection?

----

I am setting the NSArrayController to be the delegate of the NSTableView *solely* for the benefit of the menu items. Originally, of course, I just used bindings and all was well. I am not using anything as the data source, as using both a data source and bindings is probably worse than crossing the streams. The reason for setting the array controller as the delegate of the table view was to have the delegates remove: called when the NSTableView was the first responder. Now, it appears this is where I have screwed up - the delegate of the first responder does *not* appear to be part of the first-responder chain, though some items delegates are (like key window). Now, I am *sure* I saw a paragraph somewhere that said the delegate of the first responder was tested for an action when auto-enabling menu items, but now I can't find it. In any case, the docs at Apple don't say the delegate is included in that search, so using it here is entirely pointless.

So, back to the original problem - how the heck do I make menu auto-enable and perform the correct action? Do I need to subclass NSTableView to add a remove: action? Of course, that means the subclass has to find the NSArrayController it is bound to - I'm not entirely sure how do to that, though I can poke around. Or should I add "remove:" to my document sub-class? In that case, I know the array controller, but I still have to find the table view it is bound to (or rather, that is bound to it) to decide if it is the first responder. In any case, this seems like a lot more work than the case of adding a button. In the case of a button, I just made it's target/action be the array controller remove: and bound it's "enable" to the array controller canRemove:. Is there nothing similar for a menu item?

