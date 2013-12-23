---
layout: page
title: TextExtras
---

General/TextExtras is a freeware Cocoa General/InputManager bundle that enhances the standard text-input boxes of all Cocoa Applications.

It can be installed per-application, per user, or per system, by placing it in the appropriate folder.

It can be found (with source) at: http://www.lorax.com/General/FreeStuff/General/TextExtras.html

It may be a good starting point to develop your own extensions to existing applications.


----

Is this supported under Panther? The new Cocoa text stuff does a lot of what General/TextExtras does. The web site has been changed to say General/TextExtras is a bundle 'for OPENSTEP for Windows and OPENSTEP for Mach.' No mention of General/MacOSX

*It installs and functions, at least the small portion I use.*

It is supported under Panther and Tiger although "alt-/ completion" seems not to work in Tiger Mail. *Mail is now using a General/WebView for text editing, and General/TextExtras adds features to General/NSTextView, iirc.*

----

I'm writing a General/TextExtras type General/InputManager that adds subword selection to General/NSTextView (select to next capital letter, stop on underscores)

Type some multiword text into a General/NSTextView. Place the insertion point toward the middle and hit option-right arrow a couple of times. The selection is extended by words. Hit option-left arrow. The selection is contracted from the end.

I'd like to mimic this behavior in my additions, rather than always extending the selection from the leading or trailing end (which I have working fine).

I'm doing this in a category on General/NSTextView and can't add any ivars to maintain the state myself.

----

Look at General/ClassCategories - simulated ivars aren't that hard.
