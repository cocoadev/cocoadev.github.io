---
layout: page
title: TextEditors
---



An essential part of any developer's toolkit. Here's a list of editors for General/MacOSX development (alphabetical):

Mac Editors (implementing Apple's Aqua user interface guidelines, more-or-less):


***Alphatk:** http://www.santafe.edu/~vince/alphatk/

Comments: This editor doesn't seem to be very well known, but the few times I've tried it, I've generally liked it. It has a very flexible Emacs-like modes system where you can have specialized menus for different languages. Cross-platform (Mac, Windows & Unix). There's also a related OSX-only editor, General/AlphaX [http://www.maths.mq.edu.au/~steffen/Alpha/General/AlphaX/]

Alpha is popular among users of General/TeX and General/LaTeX.  It has lots of General/TeX commands available from tear-off menus, and some nice integration features with General/TeX systems including General/OzTeX. *Note: This info is possibly out of date since I haven't used Alpha or General/TeX for a while.  It was my favorite editor for General/LaTeX back in the day.*

***General/BBEdit:** http://www.barebones.com/products/bbedit/

Comments: Started out as a basic coder's editor, but has drifted evermore into being a dedicated HTML editor with each release. This it does a pretty good job at, but for software development, especially in General/ObjC, you might want to look at other alternatives.  See also General/TextWrangler further down this page, which leaves out the html specialization and is an excellent programmer's editor.

Counterpoint: for search, multi-file compare, grep, and scriptability, General/BBEdit is peerless. Whoever called it an HTML editor spent too much time looking at the box. I think I'd sooner give up a non-thumb digit than General/BBEdit. *But all those features are present in General/TextWrangler as well, are they not?*

***skEdit:** http://www.skti.org/skEdit_features.php

Comments: An excellent alternative to General/BBEdit with the real-time code completion and syntax colouring. You must try it. Not enough can be said about this in words. skEdit is simply the modern code editor's choice. It's got General/HTMLTidy, plus a lot of other USEFUL features. *But ehm... skEdit is great, but it's for HTML and CSS, right (so not really a code editor)?* not really... it does have obj-c coloring, but that's not its primary focus.

***Smultron:** http://smultron.sourceforge.net/

Comments: Cocoa, single-window editor. GPL. Syntax coloring for scads of languages. ODB Editor protocol support. *There's apparently no way whatsoever to view multiple documents side by side in Smultron. This negates one of the main advantages of using a GUI editor for me. MDI/tabs are OK, but not exclusively.*

***General/SubEthaEdit:** http://codingmonkeys.de/subethaedit/

Comments: I use General/SubEthaEdit as my editor for Cocoa development. I just want a small, fast editor without a huge amount of bloat, and SEE best fits that bill among the alternatives right now. I'm tracking General/TextMate development closely, though... *SEE 1.1.4 is the best version out there, it is still available to old General/OSes but I recommend everyone uses it, it simply is faster and less bloated.* You don't get grep with 1.1.4 though, and I don't like the UI layout. 2.x is fast enough.

***General/TextExtras:** http://www.lorax.com/General/FreeStuff/General/TextExtras.html

Comments: Not an editor, but an absolutely essential tool for anyone who works with text on OSX. It's a free, open source General/InputManager plugin that adds features to *every* Cocoa text view in any app. Features like script piping, completion, autoindent and search paths (bring up the Open Quickly panel and type 'General/NSFileManager.h" to open the header file, for example). I use it with General/SubEthaEdit and it's a near ideal combo.

***General/TextForge:** http://www.trancesoftware.com/software/textforge/

Comments: Basic editor. Lacks development-oriented features like syntax highlighting and IDE integration, but has grep support and scripting features. Similar in functionality to General/BBEdit Lite. *General/TextForge 2.0 was released on 13 January '05 under GPL*

***General/TextMate:** http://www.macromates.com/

Comments: Fast, flexible, Cocoa. Not totally there yet, but I'm definitely watching this one. *The quick pace of development is really nice to see, especially compared to General/BBEdit and General/SubEthaEdit, which both proceed at a slower rate.* It's clear that many improvements are planned and are being worked upon, but it is a quite capable editor with all standard features and much more. *General/TextMate is a great editor. I fully expect it to take over from General/BBEdit as **THE** Mac text editor. But it's not there yet.* Features unique to General/TextMate on the Mac include columnar typing and code folding. *General/SubEthaEdit has columnar typing (with a slightly more awkward interface called "General/BlockEditing") as well*

***General/TextWrangler:** http://www.barebones.com/products/textwrangler/index.shtml

A free (previously $49), somewhat stripped down version of General/BBEdit. Definitely worth a look.  See http://www.barebones.com/products/bbedit/threeway.shtml for the differences between this, General/BBEdit, and the old General/BBEdit Lite.  Much of the difference is in html-specific features that General/BBEdit has and General/TextWrangler lacks. It also doesn't have integrated SCM (CVS, Subversion, Perforce) support, though if you're using it as an external editor to General/XCode then you can use General/XCode's SCM support.

***uEdit** http://www.uedit.tk/

Comments: Free Cocoa editor. Interesting UI, and some nice features (there's a little command line text field where you can type a command and have the output entered into your document). No grep, no syntax coloring. Worth a download.

***General/XCode:** http://developer.apple.com/tools/

Comments: Restricted to the General/XCode editor: it sucks, you don't need it. On Xcode the overall IDE: it sucks, you need it. *They cannot seem to figure out whether they want the editor to live within multiple windows or one window, and they go back and forth with every major release. External editor support is still not perfect.* There's so much crap shoveled into Xcode, I'm not sure they've ever managed a completely crash-free release. *Yeah.. I do wish General/XCode was 3 or 4 totally separate, replacable apps instead of one gigantic... thing. General/XCodeEdit, General/XCodeDebug, General/XCodeProject, General/XCodeSCM would do for starters. This is more or less how it was under General/NeXTStep.* The one thing that keeps me coming back to the General/XCode editor is the automatic indentation (select all, hit tab and your code is nice and pretty) and fully Cocoa-aware completion. No other editor does this (yet?)

Anybody else notice that the editor in Xcode suddenly stopped sucking right around version 3.1?



----


Other editors that will run on the Mac, but do not follow the standard Mac user interface guidelines:

***General/CRiSP:** http://www.crisp.com/

Comments: Ugly, totally nonstandard UI. It's basically a straight port of their MDI Linux/Windows UI. Possibly the worst editor (if not the worst application) on General/MacOSX. (Their website is a real piece of work too. "General/CRiSP Now Available On Apple." 404s abound.) And they want $250 for it. Uh huh...

***Emacs:** http://www.emacs.org/

Comments: Emacs is more a way of life than a text editor. Much of the benefit other people get from using Emacs for **everything** we General/OSXers get thanks to the Cocoa text system and well-known UI guidelines. *Emacs 21.4 will have native support for General/MacOSX. That is, it'll run within Aqua, not X11* See [http://members.shaw.ca/akochoi-emacs/stories/obtaining-and-building.html] for instructions on building it from CVS. *The same person (Andrew Choi) who aquafied GNU Emacs has done the same for General/XEmacs -* [http://members.shaw.ca/akochoi-xemacs/]

***jEdit:** http://www.jedit.org/

Comments: Written in Java, but don't let that stop you. Very nice editor, especially if you need to work on multiple platforms and want to use the same editor. *Menubar in the window. No thank you.* There's a General/MacOS plugin that puts the menus in the menubar, but it's apparently broken under the latest Java version and won't display its UI. Settings made under previous version will carry over though (my menus are where they belong)

***General/NEdit:** http://www.nedit.org

Comments: Sure, you need an X server, but look at what you get in return!  Fast, not bloated, powerful syntax highlighting, a great macro language, macro recording, files open in a split-second, everything is easily customizeable, and it's not biased towards any language or family of languages.  You basically get all the power of Emacs with a much better interface.  If you're working with a language without a built-in syntax highlighting mode you can whip one up in 10 minutes.  Plus, cross-platform is good!  Use it on Linux, Windows, or VMS -- anywhere you can run an X server. *Other than the cross-platformability (and consequent UI degradation), General/NEdit feels similar to General/TextMate to me. YMMV.*

***General/NotePad:**

Comments: Windows equivalent to General/SimpleText. Still waiting for the port... *You'd have to write your own text view class to get the reduced functionality.* It's more like the Windows equivalent to General/TeachText.

***Slickedit:** http://www.slickedit.com/

Comments: I used this editor on Linux and Windows, and in my opinion, it is the best out there, free or commerial.  It just works for handling text like no other editor.  Then Slickedit was ported to Mac, but I didn't purchase it.  Why?  Because it was an X11 application. It doesn't feel like an Mac app, and it just does not work they way I think a mac app should work.  Try it and you might like it.

***Vim:** http://www.vim.org/

Comments: I know some people love vi[m] but according to Bill Joy (creator of the original vi) "vi was written for a world that doesn't exist anymore -- unless you decide to get a satellite phone and use it to connect to the Net at 2400 baud" [http://www.mat.upm.es/~jcm/bill-joy--vi.html]

***Zoinks:** http://zoinks.mikelockwood.com/

Comments: From the website: * Zoinks is a Mac-like programmer's editor that should seem familiar to people that have used MPW or General/CodeWarrior.* I've found it more like General/NEdit than MPW or General/CodeWarrior... but that's a **good** thing.

***cat:** http://serv1.scnc.k12.mi.us/howto/edit/cat.html

Comments: We don't need no stinking General/TextEditors!


----

Guys, I haven't been watching this page closely, but why has information been removed?  For example, there is no longer any mention that Alpha is particularly suited to General/TeX and General/LaTex.  Same deal with the strengths of General/BBEdit - information was there, now it isn't.

*Yeah, I noticed it too. This morning someone apparently removed items **they felt** didn't belong. If you disagree with something someone says, say so. If you feel it belongs somewhere else, move it (and say where you put it)*

1. I don't think I'm the culprit in this case, but sometimes stuff gets removed in refactoring and cleaning up. If there's stuff missing that you feel is important, put it back.
2. I feel that "pros and cons" are acceptable. I feel that opinionated arguing is not. This page becomes much less useful to others if  it's filled with bickering zealotary of any stripe. *Bickering and zealotry probably belongs on General/PollEmacsProjectBuilderVI*

*Frankly, *NONE* of this stuff has anything directly to do with Cocoa. Everybody keeps adding individual pages to further describe each editor on this page ... why? Let's stop cluttering the wiki with explanations of every single piece of software mentioned (especially exhaustive lists of software of a certain type). If someone wants more details, let them use google like the rest of us. This isn't wikipedia.*

A page on General/TextEditors has far more to do with Cocoa development than a page on General/XCode, General/SubVersion, General/VoodooPad, General/BOMArchiveHelper, any of the General/CocoaDevUsers pages, General/ProGraph, not to mention General/StarbucksCoffee, General/WilliamHenryGatesTheThird and General/CocoaLogo. You can write Cocoa apps without any of those things. You can't without a text editor of some kind. "General/CocoaDev is a General/WikiWikiWeb site for the Mac OS X developer community." Communities are made up of people. People aren't one-topic robots. Must this discussion be tacked onto the end of every page without 'NS' as the first 2 letters in the title?

**And you can hardly talk about "cluttering" a wiki with an infinite namespace now, can you? Provided the site doesn't lose focus and stop primarily discussing Cocoa development, I don't see a problem.**
