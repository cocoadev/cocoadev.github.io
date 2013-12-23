---
layout: page
title: PhilosophicalVeryLongCocoaNames
---




  Philosophical I just read a post on the cocoa-dev list which mentioned an NSTableView notification... : all of the mouthful 

NSTableViewSelectionWillChangeNotification

  Why does Cocoa/NextStep insist on such long names.  We already *know* that it's a notification (it's going to be used with a named parameter withNotification: or a method like -(setNotification) or something most likely), so that part is redundent.

  We already know that it's used with an NSTableView too, so that's redundent.  This also seemly 'violates' another principle of OO-design when applied to naming: a kind of polymorphism.  Polymorphism would say that differnet objects such as an NSTableView, NSTextView, NSEditView, etc should all be allowed to have a method such as 'ChangeSelection' which are *all the same name*..  Similarly, it would seem an extension of this principle that the notification names themselves should also have such 'polymorphism' - that the same notification *name* should be able to apply to different situation (classes/objects).  Not to say they have to be the same *underneath* (they probably couldn't be, not without rewriting a lot of how the AppKit works..)  This might mean more work for the compiler's preprocessor (figuring out what notification *really* needs to be coded), but could make code much more readable.

----
This is not a good example as NSTableViewSelectionWillChangeNotification is actually a global variable... You don't want polymorphism there!!

This is an excellent example of where we should have polymorphism, it is a bad example because our compilers will throw a turd at us for trying. All the OP's arguments make sense, we just don't have the infrastructure to implement such name changes trivially. Maybe we could make an ObserverCriteria class? JJJ
----
There is an excellent rationale for the Cocoa naming conventions:
The specific guideline for naming Notifications constants is provided here: http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html

A two-part tutorial on style conventions for Cocoa 
using Objective-C:

   http://cocoadevcentral.com/articles/000082.php
   http://cocoadevcentral.com/articles/000083.php

A Cocoabuilder thread 
http://www.cocoabuilder.com/archive/message/cocoa/2004/10/27/120289

Conventions: What's in a Name?
http://www.stepwise.com/Articles/Technical/2002-10-13.01.html

More of Apple's own words
http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingBasics.html
http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html

A good search:
http://developer.apple.com/cgi-bin/search.pl?q=naming+convention&num=50&site=(cocoa)

----

A table view and a text view have two completely different ideas of what "selection" means, which is shown by the fact that neither one has anything like a     setSelection: method. Instead, NSTableView has methods like     selectRowIndexes:byExtendingSelection:, and NSTextView has     setSelectedRange:. Given this, it makes perfect sense that they would send out different notifications, since it's unlikely that the same code would be useful for both situations, and the semantics are quite different.

You'd want to use the same notification name if you had the same semantics with possibly different implementations. You can see this in how NSOutlineView uses NSTableViewSelectionWillChangeNotification. If you were writing an NSTableView workalike, you might want to send it as well. If you were writing some kind of Finder-like icon view with completely different notions of "selection", you should use your own notification name.

*The suffix "Notification" is usually a good idea, since a constant string called S<nowiki/>electionWillChange doesn't tell you much of anything outside of the notification context. With regards to the NST<nowiki/>ableView, I personally like the Java approach a bit better, which would make S<nowiki/>electionWillChangeNotification a public constant class variable of NSTableView. ObjC just doesn't have support for these without using the global namespace or a class method. (If anyone can think of an easier way, please post here). Finally, if you use Xcode CodeSense or another completion tool(assuming your computer is fast enough), it's really not so bad. --JediKnil*

----

From Lao Tze, the Tao of Object-oriented programming

The Way that can be told of is not an Unvarying Way;

The names that can be named are not unvarying names.

It was from the Nameless that Heaven and Earth sprang;

The named is but the mother that rears the ten thousand creatures, each after its kind.

*Huh??*

