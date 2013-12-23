---
layout: page
title: NSButtonDelegates
---



Hello,

One part of my program allows the user to convert between various units of volume. The units are selected from an NSPopUpButton, the amounts is entered in an NSTextField, then the user clicks "Convert" on the bottom. My question is, once the user presses "Convert," I want the option of deciding if the action is actually run based on certain things. These certain things are that I have blank entries in the NSPopUpButton menu visually separate the different types of volume units (such as U.S. vs Imperial units). If the user selects one of these blank unit types, I don't want the convert: action sent.

I am very  new to Cocoa, but this sounds like a delegate thing to me. I've never dealt with delegates before. But I can't find any documentation on NSButton allowing delegate instance variables. Any ideas?

I realize it is easy enough to do w/o delegates, but this sounds like a good place to learn them.

----

Button doesn't take a delegate.  Only those classes that are documented as taking delegates do, and then they only have exactly the delegate methods that are described in the documentation for those classes (well, and any that are inherited from superclasses).

For your case, the proper answer is probably to use separator items instead of blank items in the pop up.  You can drag them out in IB, and they won't be selectable.  

For more complicated situations, it would be better to disable the button ([button setEnabled:NO]) rather than decide you cannot perform the action after the user has already asked for it.  That's much better feedback.  For yet more complicated situations, you wouldn't disable the action, you'd check within the action method for the case you cannot handle.  You might then use NSError and presentError: to put up a sheet describing the problem and what the user could do about it. 

----

Original Poster here.

That sounds like a very easy solution. I like it. Thank you.

but this brings up another question that I've always wondered. I can't seem to add more than three items to an NSPopUpButton in Interface Builder. I've tried nearly everything I can think of. I've been adding items at startup through awakeFromNib.

EDIT: I have found out how. I followed up on your advice and there was the Menu Item waiting for me. Thanks for your help.

