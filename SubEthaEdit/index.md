---
layout: page
title: SubEthaEdit
---



Started out as Hydra, won an General/AppleDesignAward and is arguably the best collaborative editing tool out there. General/SubEthaEdit used to be free for non-commercial use, but now is **$35**.

http://www.codingmonkeys.de/subethaedit/

Features:

* collaboration via rendezvous and internet
* customizable syntax coloring for a lot of languages
* suitable as external editor for Xcode and FTP Clients
* Blockedit (cool albeit weird feature)
* multiple views per document
* live web preview using General/WebKit


General/SubEthaEdit 2.0 was released on May 17, 2004. It adds: 


* regular expression support
* faster syntax highlighting
* integration with iChat and Mail.app
* split view in editor windows
* text autocomplete


General/SubEthaEdit is now $35, but has a 30-day free trial.

General/SubEthaEdit 2.1 was released on November 16, 2004. New features:


*Command-line tool supporting pipes and 'wait' mode.
*Authenticated file operations.
*UI for setting syntax highlighting colors.
*Improved printing
*HTML export of syntax-colored code


----

*What's "Blockedit"? I didn't see it anywhere in the menus (1.1.5), and I couldn't find it on the homepage*

(To answer my own question) It's multiple-line simultaneous editing.  See their tutorial movie here: http://www.codingmonkeys.de/subethaedit/goodies/blockedit.mov

Unique. Weird. Cool.

Careful regexp pattern find/replace can do the same thing, but it is certainly less natural.

Actually, after fiddling briefly with it, you'll find that it's simply "type on several lines at once", so that the real question is whether or not your code lines up appropriately for the changes to work.  I expect that there aren't actually a whole lot of situations where this will be particularly useful - changing types, or maybe laying out template code - but it still is pretty cool.

*pretty damn nifty. i wish they would add piping/scripting and some kind of plugin tool api. then i could ditch bbedit. (big bloated edit)*

There's already plugin interface built into OS X. You can for example install General/TextExtras which is an extension for the default text widget. It supports piping among other things. It is also opensource, so you can probably add scripting support too.

*General/TextExtras already has scripting support - you have to enable 'Run startup script' in General/TextExtras prefs, but it's not as friendly as an app feature would be. I was thinking of something like General/BBEdit has, where plugins could display dialogs to get user input, a floating palette of scripts, and so on. Also, I've caused General/SubEthaEdit to crash a few times while using General/TextExtras, which it never has without. I guess the General/CodingMonkeys are doing some wierd things with the Cocoa text system.*

Ultraedit for Windows has been doing this for some time. I guess you could tweak Emacs to do it too (give me an example of what Emacs can't do... [**Okay, Emacs can't be liked by vi-zealots (;**]). Nice feature though, wish General/BBEdit had it. -- General/TheoHultberg

----

*How are they defining commercial use of the program? Using it in a place of business?*

From the General/SubEthaEdit license:


*  Commercial License (Single Seat). The Commercial License allows you to RUN the SOFTWARE in a commercial, non-private environment.
* Non-Commercial License. The free Non-Commercial License is restricted to non-commercial / non-profit use. This includes personal, educational or recreational use, like e.g. note-talking at conferences. 


----

Eeh, I still prefer 1.1.5, it is more light and doesn't have as many bugs with its php/html syntax highlighting.

----

What bugs me about General/SubEthaEdit (as well as with most other newish editors) is the poor editing support. General/SubEthaEdit is not a source code editor, it's a text editor, and here is proof:

    
BODY {
	background-color: #990000;
	color: white;
	font-family: Helvetica, sans-serif;
}


Place the cursor after e in white on line 3. Press alt (is it "opt" in english or pc-speak, can't remember) and right-arrow. Where is the cursor now? Well, in a source code editor (like General/BBEdit) the cursor would be on the other side of the semi-colon after white. In General/SubEthaEdit (as well as General/XCode and skEdit) the cursor is after y in font-family on the line below. Why on earth would anyone want it to be there?

This behaviour is fine for General/TextEdit, in fact, if you're writing text you'd want this feature to skip punctuation and parentheses and whitespace an all that, but when editing source code such characters have significance, and should not be skipped. General/SubEthaEdit (and the others) totally ignore this fact, and it bugs me (since I'm used to General/BBEdit, and proper editing behaviour).

Yes I might be missing something, but I'm amazed that anyone would want it to work in this way, and that it has been overlooked. Am I the only one who uses this command a thousand times each day? I'd love to switch from General/BBEdit to another editor, since General/BBEdit is too expensive (yes I have paid), and does not provide the extensibility that I want, but there is simply no editor that matches General/BBEdit in editing experience.

--General/TheoHultberg/Iconara

*General/TextMate works the way you want. In fact, what constitute 'word characters' (other than letters) is configurable in its preferences. Also, it's pretty easy to write an General/InputManager that modifies this sort of thing (albeit hackishly). I should post mine sometime...*

----

February 13, 2006: SEE 2.3 is out!  Biggest new *feature*: a price tag!  $35 for any copy, not just commercial use.  That annoys me. -General/RossDude

*Why? As explained by the authors, they need to make a living since they are now out of school. If you really want the new features, go ahead and pay for the program. If not, don't be annoyed, and look for an alternative.
*

----

There's nothing stopping you from using the old version, as the developers state on their front page:

As we recognize that there is a need for a freely available and easy to use collaborative editor, we still provide version 2.2 under the old license. Starting with version 2.3 new features will only be available for paying customers however. (http://www.subethaedit.net/news.html)

----

This site is meant to be a resource for Cocoa developers. I'm not sure its an appropriate place for product promotion - even if the product is written in Cocoa. Anybody else have an opinion? 

----
When said products actually *are* resources for Cocoa developers, such as programmer's editors, they do belong here.
