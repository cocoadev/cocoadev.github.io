---
layout: page
title: HtmlDisplayFramework
---

ParsingHtmlInTextView works fine for local documents, but if you want to load a document from another machine there are issues, images not refreshing after they load for example.

There is a private framework that Apple has in /System/Library/PrivateFrameworks named HTMLDisplay.framework. If you do a ClassDump on this framework, you will notice a boat load of handy classes and methods for dealing with HTML.

There are 2 key classes:

*HTMLView
*HTMLDocument


HTMLView is responsible for using everything in this framework to render an HTML document, handle events (clicks, typing), etc. 

HTMLDocument basically represents an HTML document, this is what you hand to HTMLView to load. The HTMLDocument class can represent local and remote documents.

When link in an HTMLView is clicked, a message is sent to the views delegate. You will notice that the cursor actually changes to the pointing hand when over a link, and the text changes color. This is a feature you do not get when using an NSTextView to render an HTML document. This framework will also tell you when a form submit button was pressed and pass you an NSURL generated with the form contents.

This framework also contains a bunch of handy addons to classes such as NSString and NSURL.

I have made an example project that uses this framework:
http://www.koder.org/ghettoweb.tgz

I should probably mention how using private frameworks can be a problem because Apple can change them without warning, or completely remove them, blah blah.

Oh, and don't use this framework to make web browsers ;) 

- DustinMierau

----

Why not? Maybe not good ones, but it might be neat to hack up something Just Because You Can...

-- RobRix

----

Please, by all means, that's the first thing I did just cause I could (Ghetto Web). I am now playing around with the mozilla source and want to try to get it to use coregraphics.

I just didn't want to see 50 different web browsers on versiontracker :P

-- DustinMierau

----

Ah, right, I saw Ghetto Web immediately after I made a fool of myself here. Interestingly, it isn't CocoaDev-friendly...

But yeah, you're probably right. Dozens of mini-browsers, and none of them particularly useful...

Woo...CGMozilla would rock.

-- RobRix

----

Ah ha, so you're just afraid of competition, DustinMierau! ;D

-- SlavaKarpenko

----

Haha...I think TheOmniGroup's got him beat already :)

-- RobRix

----

Keep us posted on anything to do with CoreGraphics and Mozilla. Sounds cool.

You may want to check out Konqueror at http://www.konqueror.org/konq-browser.html. It may be easier to port. The AtheOS ( http://www.atheos.cx/ ) guy did it to create ABrowse on his own OS, AtheOS. I'd say more but the Konqueror site seems to be down currently.

-- PeterMonty

----

Konq would not be easy to port to Darwin because KDE is inherently difficult to get to work with the Darwin linker (it assumes it can do lots of things with ELF binaries that won't work on Darwin because it uses Mach-O, etc).

Mike Pinkerton is doing some work on an NSBrowserView class for Cocoa embedding, see http://www.mozilla.org/ports/fizzilla and scroll down a bit for news. And yes, Mozilla/CG would be pretty sweet :)

-- FinlayDobbie

----

AtheOS uses ELF binaries so I guess he had it easier. That and he wrote his own OS, GUI and all, so he's not exactly stupid.

I'd love to try out that Cocoa embedded Gecko but the instructions are really off putting. I really don't want to have to install fink. Has anyone got binaries of the framework? Or at least a simple source tarball?

-- PeterMonty

----

Building mozilla is actually pretty easy now that we have the proper tools (gcc, cvs, etc.) First check-out tinderbox.mozilla.org to make sure the source in cvs will compile. Login to their cvs server, check out the make file, and build it. The make file does all the work of checking out all the proper files from cvs and such. When that is done (takes a long time), you have to apply this cocoa patch and rebuild. Afterwards you compile the cocoa test projects in project builder, and everything should work. 

There are still obvious problems with the cocoa embedding project. I have found that ripping out the HTML renderer/parser is not an easy task, you pretty much have to compile in the JavaScript engine and CSS support.

Oh and apparently you have to replace the obj-c compiler, you have to email this guy and ask for his version. I forget what I had to install fink for, but I don't remember having problems with it.

Anyway, I should have time this Christmas to fool around with it more. The project is just so massive...

-- DustinMierau

----

Actually it was the Obj-C compiler thing that made me give up, did it cause you any problems? By the way I think you'd want to keep CSS support in. I see huge problems with Javascript though. Anywho, keep us updated.

-- PeterMonty

----

We don't need another browser yet.  What we need is a decent freeware/open source wysiwyg html editor with rendering.  That's what I was trying to do when I started this project http://www.geocities.com/htmlsheeteditor/index.html.  I may go back and re-do some things now that I found this framework.  If anyone's interested in working together on an open source editor with my sheeteditor as a basis, I'd be happy to yield the project to the o.s. community.  I really like the idea of using the sheet to view and tweak the html source and doing rough wysiwyg editing in the main view.

-- DarinD

----

Should we perhaps be taking the stuff from Chimera instead of using the HTMLDisplayFramework or whatever? -- RobRix

----

No way! A great source for displaying HTML is OmniHTML, part of the OmniFrameworks.

http://www.omnigroup.com/developer/sourcecode/

----

The link seems to work.  If it doesn't, let me know and I'll send it to you. ( d a r i n d  @ hou s to n. rr. com ) --DarinD

----

"Konq would not be easy to port to Darwin because KDE is inherently difficult to get to work with the Darwin linker (it assumes it can do lots of things with ELF binaries that won't work on Darwin because it uses Mach-O, etc)." says FinlayDobbie.

Tee hee.... WebKit seems to have done it.

----

That doesn't mean it was easy

- Not FinlayDobbie

Indeed.  You have a lot of Apple brainpower behind the WebKit port.  Also not FinlayDobbie.

