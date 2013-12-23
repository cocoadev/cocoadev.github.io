---
layout: page
title: TypeAhead
---

Type-ahead is that nifty feature that allows you to begin typing the first few letters of an item in a list (such as a table) and have the selection jump to that entry as you type. Much like the Finder - type the first few letters of a file or folder and it's automatically selected. I'd like to start a discussion on how one might go about adding type-ahead to widgets such as General/NSTableView. I know the Omni frameworks include this but I know I'm not the only one who would rather be shown the basics and add my own flare without depending on someone else's work in my software. Any takers?

----

Actually, type-ahead is something else, this is called type-select.  

I'm going to release (BSD-license) my type-select code soon, I hope.  I'm just working a few things out.  I didn't know the omni frameworks include it though, I'll have to look at that.  - General/KenFerry

*Never heard of it being referred to as type-select. If it's "something else" would you mind explaining what it is? ;-)*

Sure.  Here's a reference for Apple using the phrase "type-ahead" http://developer.apple.com/documentation/mac/General/HIGuidelines/General/HIGuidelines-211.html

and here's one for "type-select" http://developer.apple.com/documentation/Carbon/Reference/Text_Utilities/text_utils_refchap/function_group_13.html

* pretty clearly stated at http://developer.apple.com/documentation/mac/General/HIGuidelines/General/HIGuidelines-140.html *

I also recall seeing them use "type-select" in some release notes regarding General/NSBrowser, but I can't find it now.

You can see a version of my type-select code working in my Mail.app plugin downloadable from http://homepage.mac.com/kenferry/software.html. Since last release I've made it work properly with characters like � that take more than one keystroke to type, and I'm working on making it not block the run loop.  The class requires no configuration to start doing useful things, and it uses no instance variables, so you can pose with it.  

*
Actually - http://www.mozilla.org/access/type-ahead/ - and many other Google matches call *this* type-ahead. It's all a matter of preference, I suppose.
*

I'd say that what mozilla does is something different.  I used to call my mail plugin type-ahead, and it confused users to no end.  They expected it to be something that operated in the message text view, not in the table.  Also, mozilla renamed the feature find-as-you-type, maybe because the phrase type-ahead was already spoken for.

----

FWIW, there was a series of articles on table views in General/MacTech a couple years back that covered how do to this. The series was called 'Table Techniques Taught Tastefully' by Dan Wood (of Karelia/Watson fame). I can't find it online anywhere, but maybe you could locate a copy.

*
Thanks for the tip!  It's in General/MacTech's ftp archive at ftp://ftp.mactech.com/src/18.11/
*

----

Oh right, but the original poster wants to implement it himself.  Well, what part do you have questions on? :-)  Override keyDown to get started.

*Well, I assume that on keyDown, I'd first start a timer with maybe two seconds timeout whose callback resets a keypress buffer. As the user types, search for entries in whatever key field you're navigating that start with whatever's in the keypress buffer. In the event the buffer matches nothing, beep and do nothing. Otherwise select the first match. Now, what confuses me is how to do things like finding the nearest neighbor like the Finder does. Also, how do I tell whether or not I'm editing a cell on keyDown? Wouldn't this interfere if I attempted to do this while editing a cell?*

No, if you're editing a cell then the cell (or rather, field editor) will receive the keydown message, not your control.

*Ah. Thanks!*

For the first question, I don't find that Finder behavior to be very useful. :-) However, I'd imagine you'd use some logic about what to do in the event of no-match. Perhaps the Finder sorts all filenames alphabetically and finds the point where a file matching your typing would be, and if it doesn't exist then selects the next file. **Addendum: It doesn't have to sort *all* filenames to do this.  The complexity is no worse than what's required to make a match.**

*You're probably right, there. However, I have another question to add ... how would I handle all this with Bindings? ;-) I'd have to give the table knowledge of its array controller so it can ask it for the row data as it'd ask its datasource, right? Or is there a better way?*

This annoys the heck out of me, and I haven't found a solution.  But you can set things up so that type-select works automatically for a table that gets its data from a datasource and with minimal configuration for a table that uses bindings for data.  The alternative is to use undocumented methods to get the data anyway.

*Well maybe "if datasource != nil then search this way, else if arraycontroller != nil then search this way, else give up and cry - That's the best solution I can think of at the moment.*

.. and that's pretty much what I did.  The main difference is that I did it with a delegate method,     typeSelectTableView:stringValueForTableColumn:row:.  This is because it's still only one line of code for the delegate to use the arraycontroller to supply the data, and this also opens up the possibility of using type-select for things like images.  Maybe.  Not like I have a use case on that. :-)  The delegate method is optional, and the table uses introspection to get data if it doesn't exist.
