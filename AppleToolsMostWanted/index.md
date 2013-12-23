---
layout: page
title: AppleToolsMostWanted
---

Part of CocoaDevelopersMostWanted (It would be cool to have a category "Most Wanted" or something, no?)

**If you want any of these changes, please submit them to [https://bugreport.apple.com/cgi-bin/WebObjects/RadarWeb.woa Radar]. Simply posting them here is great for discussion and raising awareness, but submitting them to Apple's bug tracker is what will get them implemented. In addition, the more people submit a given issue, the more likely Apple is to do something about it.**

**XCode**



*Smarter smart groups. (yea: 1 nay:0) The only options currently available are simple and regex filters on the file name. Spotlight should make it simple to create groups based on all sorts of things, such as: "All .m files in the project not modified since noon today" or "All image and sound files in the project." You can do this easily enough in Finder, and since Xcode uses the same icon for smart groups that Finder uses for smart folders, I'd guess Apple might be heading in this direction. But I think of new ways to that I could use a feature like this in Xcode two or three times a day.

*refactoring tools. (yea:14 nay:0) Please! The primary advantages to using an IDE over a straight text editor are code-completion (which we now have - hooray) and refactoring (which we're still waiting for!). Please add refactoring soon. "All the other IDEs are doing it!"

*Tabbed Editing. Good gods, tabbed editing. I work on a huge project, and switching between files requires way too much mousing. (yea:3 nay:1)

*nay explanation: The files are already listed to the left in the main window and clicking one changes the file you're editing in the main window. Even though the list doesn't look like "tabs", it's acting like them - toss in grouping(etc) and the list is more functional too. So, forget tabs - edit (mostly) in the main window and use the list as a vertical tab bar - long live the tabs we already have.*

"nay rebuttal: You rearrange your entire project in the order that you're working right now, *and* only have like 3 files visible in it at each time? That seems like a lot of work. Tabs are neat to have because then you have only the file you're working on right now and you have them accessible by just a key combo"

*rebuttal rebuttal: Huh? I don't have to rearrange anything. I have way more than 3 files visible at a time too - they're listed right there under "Groups & Files". *pointing* I see what you're really asking for though; key combos that'll switch between files. You can ask for that without asking for tabs. But then there's the back/forward button (cmd+option+left||right) that already (with limitations) does that - I think I use that more than the Groups & Files list. Anyway, I still don't think tabs are a good idea because they take up too much room and would almost always spill over into a menu (yuck).*

*auto-synchronize method/function signatures between interface (headers) and implementation (yea:13 nay:1) (isn't this just a specific use case for refactoring?)

*hide/reveal method bodies in normal source view (keeps me from having to use the pop-up menu to see my own methods) (yea:11 nay:0)

*why are there "executables" and "products" in the tree? Can these be merged or replaced with something more self-explanatory? (yea:8 nay:1) Some "products" like plug-in bundles are not "executables" this is for multi-target projects

*new build phases: PackageMaker and dmg creation (with a GUI supporting all the features of the CLI tool) (yea:8 nay:0) *It's not from Apple, but there are third-party tools for this. DropDMG is my favorite. There are free ones as well.*

*I would be perfectly satisfied with just getting the stuff that it has now to work properly.  The thing crashes like no other, it's ridiculous.  Not to mention it has redraw errors in the text editor.  Also, syntax coloring doesn't work so well, case in point: comments.  UGH. (yay: 10: nay: 0) *I'm just one yay, but I'm shouting it* **'I'm just another yay, but I'm screaming.**' ** -- People should be ranting about this. It's like Xcode was written by M$.** (Ouch, you should try actually using Visual Studio before saying things like this. It's a fantastic IDE and contains virtually every feature people are asking for in this list, plus tons of other stuff) *I'm holding out high hopes for TextMate.*

*static code analysis/QA tools (yea:4 nay:0) <-- MS Visual tools have some static analysis tools in the IDE. Java tools like Eclipse do too!

*Third-party language plug-ins (yea:4 nay:0) <-- I want Eiffel...(oh there is... well make it better) -- full python support! Hasn't this already been done? Xcode can edit any filetype, so all you have to do is make a project that ends up being a template, and configure the build phases correctly. You can do shell scripts in the build phase so theoretically you could make your own compiler and call it from a build phase.

* Distributed build using non OS X machines through XCode (yea:1 nay:0) So I can use my linux box for building things instead of dust collection. **Done already.**

* More support for Lex and Yacc (yes, I do have Xcode 1.2, but I haven't been able to get it to work with the y.tab.h file), including syntax coloring (yay: 2, nay: 0)

*adorn variable names (color/font/style) to indicate extern/file/instance/local/undeclared (yea:2 nay:1) + class syntax highlighting
What are some examples? *I don't know if this is what was meant, but there's AutoGraf*

*some kind of meta-indexing or shared indexing: indexes for libraries/frameworks shared between projects, not re-generated for every project (yea:1 nay:0) <- not needed, see IndexTemplates

*Xcode to stop disabling the damn build buttons and menu items, etc..., after finding one warning in my code (like an unused variable), and causing me to close the project and reopen it. (yea:4 nay:0) <- Don't have to close the project: just make a  build using the menu and the button will come back * So and friggin what.  It's a bug.  They need to fix it, not to mention build train hangs*. **<-- Does anyone know if this has been fixed in the Xcode 1.2 beta (I dont have it)? **  Erm... I have never had this problem, even with Xcode 1.1. -RossDude

*"flat views" of obj-c classes (shows all methods/instance vars... including those inherited)
*I'd love to be able to use XCode as a general-purpose text editor, but can't because it's so bloody slow.*

* Pluggable Editors (emacs or vi instead of that piece of crap). *This is there, to a certain extent - you can set XCode to open files in any editor you want via preferences. You could presumably hack something together with Platypus or iHook to open files in emacs/vi* *XCode (v. 1.5) has external editor support for emacs/gnuclient*

* Ditching the multiple window concept (or it being configurable). The class browser should be in the same window, and all the smart group bar(s) should be completely closable/hidable. *Xcode has been pretty configurable w.r.t. single/multiple windows for quite a while now. Unless you're really picky, I'd call this one done.*

* A keyboard shortcut to move the cursor in each split views: i use a lot of split views when editing my code (the above split view is A.m, then bottom is B.m) each time you want to pass from A.m to B.m, you have to use the mouse! * **-- Already existing : ctrl+tab to navigate between split views --** *


**InterfaceBuilder**


***Symbolic Tags**- 
It would be great to have symbolic tags in IB.
Take a look at this post on cocoa-dev:
http://www.cocoabuilder.com/archive/message/cocoa/2009/2/26/231103 
If you're interested there's some good discussion in the thread- this particular post is about a third down, but there's good posts above and below

*Java Swing source code generator. Take a nib file and generate Java source code - for those that want to make cross platform app (native cocoa on MacOS X, java on other platforms). (yea: 5 nay: 4)

*Why would this be part of InterfaceBuilder? Maybe an external, 3rd party tool, but I don't think Apple should bother with this.*

This exists: http://www.nib4j.com/

* Add the support of NSSegmentedControl. * Actually, support was added in Xcode 2.0's version of IB. Get it at the ADC. [http://developer.apple.com/] *

* Drag & Drop NSToolbar support.(yea:4 nay:0) **When I first started doing toolbars, way back when, I though: "gosh darn it, why doesn't IB do this?" but as the years went by, I think I realized that toolbar validation and dealing with different tool bar items at different app times, would essentially make you reprogram all the methods IB would do for you, with maybe the exception of the default set...**  Unless you use a highly static toolbar, the best IB could do for you is autogen the few lines of code it takes to get a toolbar up and running.  There are bigger fish to fry in my opinion.

Well, for those of you who still want this, download GenericToolbar, a free and mostly bug-free IB palette written by me to address this problem. Also, any news from Tiger users on this issue? --JediKnil.

It's in Leopard. --Amorya

* Support for custom bindings (currently only supports bindings on Apple's classes) (yea:5 nay:0)

* Allow multiple selection for the springs resize panel.  Moving a group of controls from one side of a window to the other is a real pain, doing a lot of clicky-clicky to select each item and change (for instance) two spring settings to change the gravity from the left side to the right side. *It could also be useful selecting multiple objects to change other common settings like alignment* (yea:3 nay:0)

*Applescript support for creating/editing objects/Applescript Info (yea:1 nay:0)

*Context sensitive help for controls. There are times (especially when new to Cocoa, but not only when new) when it would be nice to get 1-click access to a summary of a control's features and attributes (as well as some basic how-to info, supporting OS X version(s), and so on). (yea:2 nay:0)

*Custom cell/control designer. I'm bored of the standard widgets and want to make new ones by taking existing ones (image cells, textfield cells, button cells etc) and making composite cells. Such composite cells would be a great alternative to generic tables, which only give you a single layout -- horizontal rows. Lots of programs already have primitive attempts at such a thing, either by special case classes or even using view hierarchies interior to cells (eugh). Given a generic class design and a means to specify layout, a single composite cell class could suffice, especially if it supported a recursive hierarchy of instances and a variety of simple layout rules which could also be applied recursively (rows, columns, boxes; left-, right- or centre-horizontal aligned; top- bottom- or centre-vertical aligned, etc.) Well maybe I'll get around to it myself one of these days. (yea:1 nay:0)

***(2004.09.30)** Hide or disable GUI items in the **Palettes** window (and attributes in the **Info** window) if they aren't available for your "Oldest [OS X] Target." If necessary, make this switchable via a preference.  **(yea:1 nay:0)**

*Ability to cut and paste controls (e.g. to move them between tab views) without losing their connections.

*Ability to right click to make connections (With a mighty mouse, for example). (yea:1 nay:0)



**AppleScript**
* Add a more programmer-friendly OSA language than AppleScript. A la Frontier 4.x.

*There's binding for JavaScript - [http://www.latenightsw.com/freeware/JavaScriptOSA/index.html]. I believe there are perl bindings out there too.*

A selection of OSA language components: [http://homepage.mac.com/philip_aker/osa/osa.html]

Apple Event Manager bindings for Perl: [http://search.cpan.org/~cnandor/Mac-Glue/], Python [http://appscript.sourceforge.net] and Ruby [http://rb-appscript.rubyforge.org]. There are also Tcl bindings, but don't think they're as advanced. -- has

**PerformanceTools**

**GraphicsTools**


*A simple icon editor (yea:2 nay:3) [There already is one: Developer/Applications/Utilities/IconComposer] �No, this is not an editor, just an importer. *How to make a simple editor for 128x128x32 icons with alpha? Might as well just use Photoshop.*  As the naysayer, I just want to say that graphics design is simply beyond the scope of software engineering.  I second the photoshop comment. *Sure, $650 is reasonable to make a bloody icon. Not.* Iconographer is a pretty nice alternative for small icons, but if you want a good-looking 128x128 icon, a full-out graphics editor is the way to go (i.e. Photoshop).

Re: the $650 comment - So use GraphicConverter ($35) or the gimp ($0). The point is, for OSX icons the only difference between an icon editor and a general purpose image editor is canvas size.

Because of both the resizable nature of OSX icons, and the general look and feel that seems common to them all, I think it's easier to at least start by copying the paper sketch of the icon design in a vector package anyway.

Speaking of vector packages, is there an equivalent of the gimp or GraphicConverter for that kind of thing? I'd love to buy an Adobe thing, but I just shelled out for Elements (for other reasons than icon-making). *There's Intaglio [http://www.purgatorydesign.com] and Create [http://www.stone.com/] And nobody's mentioned the $15 Iconographer [http://www.mscape.com/products/iconographer.html] Doh. Nevermind. Someone did mention it. But they didn't post a link, so I'm leaving my comment.*

"For Open Source vector graphics, there's Inkscape. There's a Fink package. It's oriented towards SVG, and works nicely with SVG-based KDE icons."

Ultimate free pixel pusher: Pixen http://www.opensword.org/Pixen/



**Terminal.app**


*New feature - dropping a folder onto Terminal.app's icon should open a terminal window to that folder. *It already does, just hold command-option while you drag to force it to accept the drop.*
* **Tabbed** Terminals - *coming in Leopard*


You might want to try http://splodge.fluff.org/~phil/terminator/ (nightly builds from http://www.jessies.org/~enh/ ). It looks very like Terminal.app but does support multiple tabs, and dropping files if not folders. You can also use it on other Unixes if you find yourself missing Terminal.app there. (That was one of the reasons we wrote it.)

**Utilities**

* A code formatting tool, similar to indent, that works with Objective-C code. Or at least the ability to script this in XCode.


**Refactoring Tools**

* Refactoring tools for Objective-C (if possible -- C++ and Java too ). See the refactoring tools in Eclipse and IntelliJ IDEA. See also www.refactoring.com.


**Other**

* A WYSIWYG HTML editor based on InterfaceBuilder. I only dream of one written in Cocoa for stability. IB has the ability to layout UI objects for applications, and I'm sure the talented engineers at Apple could rewrite the engine so that it parses and produces HTML, XML, etc.. Plus they could use WebKit for a perfect preview. I think that Apple would find something like this to be wildly successful, and even if it wasn't free, I would definitely pay for it. (yea:2 nay:1) *Apple would then have to write a Photoshop-level image editor and a page layout program, since this would surely be the last straw for Adobe. The public beta did include a wysiwyg html editor, but it was pulled after that.* **<-- You see I don't think that Adobe would drop it's Macintosh product line. I would say that easily 50% of Adobe's desktop publishing (i.e. Illustrator and Photoshop) customers are using Macintosh. Macintoshes running Adobe apps are used heavily in marketing/advertisement. I simply don't think that Adobe could afford to take a hit like that.** ""<-- Adobe should not be the answer to what we can and can not use. It has been several years now for OSX, why has no one at all developed this app? "" *Because it's a big, commerically-oriented project, and most big commercially-oriented developers are using Carbon. A small independent developer (most likely to be using Cocoa) probably isn't going to write a WYSIWYG HTML editor for his/her own use. If you want one, get started on it - you could be the next Adobe ;)* ** Why the hell not? Why not create a SourceForge project for this idea? ** "I (the original poster of this item), have actually as of late been working on starting to clean up the code for Gorm (the GnuStep project's open source version of InterfaceBuilder) to maybe use as a basis for a Cocoa HTML WYSIWYG editor."

Now we NEED this, with Dashboard and all. HTML is easy, but for any decent interface-d widgets? One of the reasons IB is better than simply editing code is design. Which do you prefer? Coding-then-checking or an actual, productive environment. Dashboard would then be a first-class citizen in the Tiger world. In my mind, right now it isn't. No XCode project, even! Alright, well that's not too bad, but it sort of shows how much apple cares about it...

*Amen. And an easier way to add Cocoa UI to widgets would be nice as well -- presumably this would be included.*

If you know how to code why would you want to develop a Dashboard thing? It's not a first-class citizen; it's a way for the code-challenged to write stuff, like HyperCard was. And I think the Dashboard guidelines specifically recommends *against* using Cocoa/Aqua widgets. It would be better to do a separate IDE for Dashboard, instead of trying to glom stuff onto IB

>> You mean like Dashcode?

Yes. Dashcode, ( http://www.apple.com/macosx/leopard/dashboard.html ) is a simple development environment for Widgets. 

>> How about a Cocoa version of Nvu, like Camino is a Cocoa version of Firefox? - Pietro Gagliardi, http://web.mac.com/pietro10/ 

You know what? I think I'll try it. I already did in Objective-C and it was a pain. I feel more comfortable with Java. So I will give info on my website when I get to it.  Java won't work, looks like it's off to Objective-C school for me! - Pietro

The likely reason why there is no cocoa version of Nvu is that such a project would be too much "design up front". Also, most of the people capable of writing such a tool are happily using Vim or Smultron and have itch to scratch.

I would guess that a cocoa CSS editor would be a better bet.

Also, see Sandvox http://en.wikipedia.org/wiki/Sandvox (the Sandvox site doesn't have a page that focuses on this project)




----
I vote we separate out the requests that are implemented in Leopard to a separate page.

