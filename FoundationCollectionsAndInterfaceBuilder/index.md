---
layout: page
title: FoundationCollectionsAndInterfaceBuilder
---



Has anyone aside from me been bemused by the total lack of support for Foundation collections in IB? You cannot add anything to them, see their contents, and in many nibs even instantiating them is disabled, for no visible reason. And yet I know for a fact that NSDictionary requires no hacking to fit in a nib, and creating a new connector type to add nib objects to a dictionary is trivial, a ten-line segment of code.

What is missing is any inspector classes to make the trivial possible. And with IB's chronic lack of decent documentation, making them is rarely worth the time for busy coders.

I have coded a set of inspectors for an IB palette that enables NSDictionary and NSArray in nibs; go to FoundationCollectionsPalette for more. (This is the *old* support page.)

-- KritTer

OK, that rocks! -- JackNutting

----
**Feature Requests**
----

I would personally be *very* interested in getting this to work. It would make an excellent addition to InterfaceBuilder, especially if eventually you could populate tables, et cetera, with arrays or dictionaries (but that's probably something that would require Apple's participation if it's even possible).

I look forward to seeing this!

-- RobRix

Sorry; by "populate tables", do you mean making NSArray(s) or NSDictionary act as a data source for an NSTableView? That would need only a trivial intermediary class, easily possible. Or do you mean set up an NSTableView in lieu of a data source? But then the table would have no way of storing its changes. Or do you mean setting up a table *before* it uses its data source? That would require setting up the data source also, which would obviate the need for the table to know anything about it. If none of these, what *did* you mean?

-- KritTer

I meant the first, but you could create the array/dictionary in IB. I'm trying to remember the specifics of what I had in mind, it was more complex than this... I'm not sure if it would be possible with IB or not.

-- RobRix

After I get NSArray working, I'll look into writing a general class to do this. And trust me when I say that most anything is possible in IB :)

Addendum: I tried my hand at such a class, but it's a little more trouble than it's worth. I may come back to it in the future when Apple sorts out issues with frameworks...but probably not. If anyone wants the code I've done so far (everything but undo), just get in touch.

-- KritTer

It would simply be really cool to just make an array or dictionary of whatever right in a nib via IB and then do whatever with it. Maybe just for testing before you get around to writing a document class. Heck, even to just add random strings would find a use, I'm sure, like for switching names on things or whatever without using string constants in code or having to cope with localization, you know, when you're just hacking. Sure it sounds pointless but it would be fun. � Brent

