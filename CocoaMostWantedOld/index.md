---
layout: page
title: CocoaMostWantedOld
---

--------

This is an archive copy of the now retired C**'ocoaMostWanted page. See CocoaDevelopersMostWanted for a list of the current "most wanted" pages.

--------


This is a place to voice our most wanted Cocoa features to Apple (or most wanted bug-fixes). Add your votes and re-order if necessary.

All, this is getting really unwieldy. What say we move all of this content to an 'oldies' page, establish a new Wanted page with some style guidelines?

Sounds good (4), no way (0)

Your wish is my command. Here is a new page: CocoaDevelopersMostWanted � go there and help me make style guidelines. I'll try to start with an outline, sub-pages, and a couple of items. �BrentGulanowski

Page size was over 32k - as an interim fix I moved the Fulfilled Wishes section to CocoaMostWantedFulfilledWishes and the Denied/Impossible Wishes section to CocoaMostWantedDeniedImpossibleWishes
----**Cocoa / ObjC**----


* *Regular Expression Suport*. (Votes: AYES: 4 NAYS: 0 MAYBE: 0) 
How about adding built in regex support to Cocoa?  That way people wouldn't have to defer to third party options such as embedding perl code, or using MoKit, etc.  This seems like something which is useful enough that it should be built in.  Even languages like REALBasic have this included! 

:What about regex.h?  Try man regex.


* *Give us some Apple sanctioned means of running Cocoa apps on other platforms.* (YellowBox for Windows, OS X on other platforms, or whatever). (Votes: AYES: 15 NAYS: 6 MAYBE: 1) - This would really make Cocoa more desirable! And could also cannibalize Apple's hardware sales.

Hum, nobody knows the existence of the GNUstep project http://www.gnustep.org ?
If you use OpenStep and not too much Apple extensions it's not too hard to have a code compiling both on Mac OS X and on GNUstep (linux/bsd, windows and beos in (slow) preparation)

gnustep is barely a blip of activity.

I think cross-platform Cocoa can only help them expand mindshare - possibly even help them when they *cough* port Mac OS X to x86 hardware *cough*. If anything, more people will be willing to code with it, thus more apps will run on the Mac OS platform. --JLOwens

I think the result of making yellowbox for windows would be that most (if not all) cocoa programs now running on the Mac would also become available for Windows but I doubt that any great number of window developers would make a switch from .net to cocoa. The net result would be more programs for Windows and less unique Mac applications which in turn would hurt Apple's hardware sales which is what make them go around in the first place. But that's just my  theory... --Gabbe

<Heavy rumour hat on> Perhaps the reason Apple has apparently been playing down Mac-specific (actually HFS-specific) things like alias records (in favor of paths) and type/creator (in favor of filename extensions) is that they're planning at some point to revive YB for Windows? Now that Apple is apparently through licking Microsoft's boots to ensure the continued development of Office/IE for the Mac, the way is largely clear for them to do this.

i think this would be the single most important thing for macosx adoption --absinth

Windows can go to hell and die. If you want a Cocoa equivalent on Windows, make it yourself or tell Micro---- whooops you didn't hear that from me. Make great Cocoa apps for Mac and increase the desire for Macs and Mac OS X. Let them eat Visual Basic. �Brent

* *Allow NSToolbar positioning on top or left of window* (AYES: 2 NAYS: 0)

Wouldnt' work for all things, but if all you're using a toolbar for is file stuff & drawers the option is nice

* *Add automatic GarbageCollection* (Votes: AYES: 7 NAYS: 16 FENCE: 2) [see GarbageCollection]

* *Objective-C EnterpriseObjectsFramework!* (Votes: AYES: 10 NAYS: 0) 

* *Provide source to Cocoa* (Votes: AYES: 4 NAYS: 3)
* *Provide a publicly viewable buglist for Cocoa* (Votes: AYES: 8 NAYS: 0)

You can see your own bugs, at least. But yeah, they should show ALL bugs, at least to paid ADC members. And you should be able to add a vote to it as a priority to fix. Democratic feature requests, anyone? �Brent

*The issue here is bugs that contain sensitive or confidential data. Developers may be more reluctant to submit bugs with test cases if they can be viewed by just anyone.*

* *Start treating Cocoa as if it is the #1 development environment for Mac OS X, instead of just saying that it is!* (Votes: AYES: 6 NAYS: 0)

What does that mean?

**Add namespaces to the Obj-C language (directive @namespace would be useful)* (Votes: AYES: 9 NAYS: 0 MAYBES: 2)

Never been able to figure out what a namespace really is...

A namespace is a formal grouping of object names, such that you can refer to the group as a whole by a single name. A class is a namespace. What's being asked for here is the ability to have arbitrary namespaces, so that you could have a class named, for example, MyName::Object instead of MNObject. There is also in some languages a way to say "assume that I'm working in the MyName namespace," so that you don't have to type "MyName::" as a prefix in the implementation of MyName::Object. This is far more flexible and scalable than the two-letter prefix which currently disambiguates different names.

*Add the undocumented NSMethodSignature method +signatureWithObjCTypes: to the API. More generally, make the creation of arbitrary utility objects easier! (Votes: AYES: 2 NAYS: 0)


* *First class closures or blocks ala Smalltalk/Ruby* (Votes: AYES: 2 NAYS: 1)

Obj-C isn't designed for closures. It won't really make good use of them without a proper functional infrastructure.

What are you smoking? Brad Cox himself added closures to Objective-C twelve years ago. It just never propogated up to NeXT. See HigherOrderMessaging on this site for examples of what it would be used for.

Speaking of HigherOrderMessaging, I'd like to see Apple add it to their API's.  jd

* * Bring in Multiple Inheritance.*(Votes: AYES: 1 NAYS: 5)
Sometimes protocols just don't cut it, and it's really annoying having to add that same bit of code all those times even though it doesn't change. C++ proved that you can have your cake and eat it too.  If oyu didn't have multiple inheritance there was virtually any loss of performance, and if you decided to you could.  I know a lot of people don't like it, but why not just have it there for those of us who like to occasionally use it when correct.  The whole diamond issue isn't even that big of a deal, there are several solutions and we can just pick one and use it.

I'd prefer instead the addition of Ruby style mixins.  They're like Objective-C categories, but they can be shared between multiple classes.  jd

* * Improve NSAffineTransform/CGAffineTransform to allow for Projective Transformations.*

Sure, scaling, rotation, and translation make a lot of sense and are highly useful, but why not allow the extra paramaters (the third column of the affine transform matrix) to be changeable. This would give us projective transforms, so we could make an object look like it is being shown in a kind of perspective instead of looking at it flat on. Of course, this would have to be changed in core graphics and makes the matricies possibly non-invertible, but other features come along too. (Think along the lines of of sun's project looking glass).


----**OS X User Experience**----


* *Bring back a renewed version of System-7 sounds...* (Votes: AYES: 2 NAYS: 0)

It just annoys me that everytime I want to play a sound, iTunes launches and the sound is added to my library...

Easy workaround: Select the sound file, choose "Get Info...", select "QuickTime Player" in the "Open with..." area, click the "Change for all" button. Suddenly, all such sound files will be played by QuickTime player, which doesn't add anything to any library.

Easier workaround: Select the sound file, switch to column view, play the sound in the preview pane.

Easiest workaround: Select the sound file, hit Command-I (or Command-Option-I), play the sound in the get info window.

* *Real text dragging* (the Mac way). (Votes: AYES: 11 NAYS: 2)

It should delay on both. I'm sick of having text move by 1 pixel when i try to place an insertion point.  - fine, if you want to add a delay for text dragging, make it clear when you're able to drag.  I can't count 400 milliseconds, can you? This is really fixed...It is just that Cocoa applications need to draw to their dragging image the correct way...sorry, but fix it yourself.  No it is NOT fixed.  You have to hold down the mouse button for an arbitrary time in order to be able to drag.  There is ZERO feedback (none, nada, zilch) that this time has elapsed.  If I'm dragging a lot of text around, I want to have the computer respond instantly to my drags, not after 400 milliseconds.  If some other modifier needs to be used (e.g., space bar which the OS 8.5+ Finder uses to trigger immediate window pop-open) that's fine.  Personally, I've never been tripped up by the Carbon behavior, but that's just me.

- Note from PaulBayley :
The delay is STUPID! Either people want to use drag+drop text or they don't. The delay ought to be reserved for a contextual popup menu instead.

- Trax's answer :
What if you already selected a portion of the text and then you want to change your selection ? With no delay, you would drag&drop instead of starting a new selection. I think it can be annoying...

In Carbon this is solved by simply clicking again. If you intend to click, you don't actually move the mouse, expcept maybe for four or five pixels by accident. This usually isn't enough to cause the dragged item to move. If you clicked on a selection after such a "pointless" drag, the OS would take the hint and see it as a click to change the selection. Carbon's behavior really *is* better in this case (I hate to admit it, but it's true), because it follows the user's natural reflex of "The computer thought this was a drag, damn, let's try this again" -- UliKusterer

**Improve Window behavior.* (Votes: AYES: 1 NAYS: 1)


* Don't raise a window automatically when clicked at (makes working with many windows a real mess).
* Either accept first click in all Windows/Apps or none. (e.g.: iTunes: if you click on the play button in the inactive window at full size it activates the window, if you click on play on the small one it starts playing).


- This was actually a feature added on, they made a big deal about it.   They said "now when you have it in small size you can choose your music without switching apps".

I only agree with the last one.

- Here's Apple's word on the subject: (from the HIG)

Click-through is not a property of a class of controls; any control could support click-through in many contexts, but the same control could disable click-through when its use could be destructive in a particular context.

In an inactive window, an item that provides click-through should have its text or glyph (such as an arrow) in 100-percent black; if the item usually has color (such as a radio button), it should be colorless in its click-through state. Items that do not provide click-through should appear in their disabled state.

* *Use aliases instead of paths.* (Votes: AYES: 2 NAYS: 0)
At least, give us NSAlias class.
Though I do agree with a poster to CocoaDev who said that aliases should check paths first, rather than last. 

*note from PaulBayley
An alias is an arbitrary description of how to find a file. You can resolve an alias any way you wish. If you want an alias to behave like a symlink you just have to remove (make null) the FileID and host FolderID.

Paths ought to be replaced with not only Aliases, but FSRefs (or equivalent) depending if the file primitive is being stored (or used for IPC), or if the file primitive is being used at runtime respectively. When I use FSRefs for my runtime file primitive structure it doesn't loose track of the file, even on UFS volumes. If I tried to do the same with Aliases I don't believe it would work.

* *Have a system-wide functionality that brings up contextual menus after holding down the mouse for a certain period of time instead of only working with control-clicks.* (Votes: AYES:1 NAYS:1)

No, this is not suitable for all apps. Which means it will be applied inconsistently.
If right-clicking is important, buy a two-button mouse...

* *Stop bringing to the front the windows of certain applications when I click their close boxes! Safari and iChat both now have this problem. Being able to silently close background windows used to be one of the coolest features of os x.* (Votes: AYES: 2 NAYS: 0)

This was covered up above, but it was lumped in with some weird stuff.

Work Around: Hold down the command key when you click the close box.

* *Un-deprecate the deprecated NSMenu... classes, replacing the Carbon menu manager with the one from NeXTStep, and making NSPopUpButtonCell (a subclass of NSMenuItemCell) no longer a paradox (being a still-in-use subclass of a deprecated superclass).* (Votes: AYES: 0 NAYS: 1)

The Carbon menu manager is more powerful than the NeXTStep one. Apple should include more of its features in NSMenuItem.

* *Bring back the other style of NSSplitView bar. Only the bar with pin stripes exists in 10.3.* (Votes: AYES: 2 NAYS: 2)

I think Panther's appearance is much nicer than Jaguar's, and only having 1 split view is part of that. Both controls did exactly the same thing, why the need for 2 appearances?

*If one of the views contained inside the split view has a pin-stripe background, then it makes the bar very difficult to see. If both views have have a pin-stripe background then it is nearly impossible to tell there's a bar there. This was not a problem with the other style.*

Panther's appearance is much cleaner and less visually distracting than Jaguar's. There's still the dimple in the center to indicate resizability - no different than a window's resize indicator.


----**ProjectBuilder** / **XCode**----


Maybe a bit contentious this, but * * dump project builder altogether * in favour of something like netbeans (http://www.netbeans.org) or eclispe ( http://www.eclipse.org ) I know OBJ-C purists here will be offended by using an IDE written in Java, but both of these IDEs are massively much more powerful than project builder and could be extended for use as Objective-C/ Cocoa / Webobjetcs tools much more easily than writing project builder from scratch. Apple could leverage the enormous amount of work going into these IDEs and focus their efforts on better compilers and debuggers. (Votes: AYES: 1 NAYS: 0)

* *ProjectBuilder should have some code generation for easily adding class and instance properties and accessors* - and some refactoring capabilities, please! See Smalltalk! (Votes: AYES: 10 NAYS: 0)

Check out the sample user scripts menu stuff.  One of the sample scripts lets yoiu auto-gen accessor decls or defs from selected ivars.  Not perfect, but a big help.

* Wanna check out my lil forum thread XCodeTooMuchLikeATextEditor ? (Don't vote on this one) 02/18/2004*

Accessorizer handles this quite well. I don't miss this in PB/Xcode at all.

Native support as part of larger refactoring-style features would clearly be cool, though.

*see Also RefactoringInCocoa*

* * Open Source ProjectBuilder!* (Votes: AYES: 2 NAYS: 0)

They have, like, two people working on it. No wonder it's -- excuse me, but I have to say it -- a joke. Especially for WO developers. It's embarassing to introduce WO to new java developers, the tools are so primitive.

Why not open source them? 

* *Dock icon progress bar of compile progress--like in toast. Would be fun and useful.* (Votes: AYES: 2 NAYS: 0)

* *Improve the editor!* - The old PB editor had code folding, much better indentation, better coloring, faster completion, better navigation, and was just much faster overall. It seems like Apple has been focusing on things like predictive compilation, distributed builds, zerolink and so on, while the editor is only marginally more capable than TextEdit. (Votes: AYES: 1 NAYS: 1)

I haven't noticed that much of a difference. The XCode editor seems fine to me. I'd rather they spent resources elsewhere, like the language plug-in api.
 

----**Other**----


* *mailing list - Add capability to not garble non US characters* - isn't OSX supposed to be international? (Votes: AYES: 8 NAYS: 0)

I want my Unicode, dammit!

* *Support/allow OtherLanguages* (Votes AYES: 4 NAYS: 0)

? I'm thinking about PythonCocoa, some kind of RubyCocoa already seems to exist, and was it that Perl people are at it too? (Yep - we are. See CamelBones) How about C#-Cocoa?

*There is in fact a project for Cocoa and Mono (.NET) integration: http://www.cocoasharp.org/ *

TOM Cocoa would be interesting

Apple appears to be working on a language plug-in architecture. They already have keyword recognition for many languages, including DyLan. Not granted yet, but they're working on it.


----
----
CocoaMostWantedFulfilledWishes

----

----

CocoaMostWantedDeniedImpossibleWishes

----

----**Further comments**----

**[note1]** - Some info about Other Books!


* Apparently there's a much better book than "Learning Cocoa" on the way from the guy who runs BigNerdRanch. -- FinlayDobbie

* Having seen a pre-release version of Hillegas's book, it is very impressive and easy to read, but it may not be the book that everyone is waiting for. You need to know C and you need to have thoroughly read and understood Apple's Inside Cocoa, Objective-C language book from FatBrain. Aaron's book is aimed at programmers, not novices --WmMoss

* One excellent NeXTSTEP programming book and one book on Rhapsody's Yellow Box are being revised for Mac OS X. -- ScottSteinman


OK, now just jump on over to the CocoaBooks page for some info on published (or soon to be) books about Cocoa Programming.  ---> K. McClanahan

**[note2]** The Darwin Project, http://www.opensource.apple.com/, is using the Doc Book DTD for its open documentation project and accepts/solicits contributions

**[note3]** I vote against user contributions for this reason: In my experience, most programmers don't know how to write in a coherent and understandable way. Want proof, look at the number of circular definitions in the CocoaGlossary section of this site.

**[note5]**
Although the major source of ProjectBuilder slowness is jam and its orphans lying underneath the GUI, it also adds a great bit of slowness if you set it to index the source files (and that's on by default). Besides, jam's multiple processor feature is broken since 10.0 (according to some Apple engineers), so it's always using one processor.... Fun. --SlavaKarpenko

**Outcome:** As of Project Builder 2.1 (December 2002 Developer Tools), jam is dead! As well, multiple builds can be run on different processors!

**[note6]**
MachO is the binary format and ABI used natively in MacOS X. Although it works, it's somewhat slower than, for example, CFM. The reason is that it was designed for CISC processors (m68k, x86), and does not follows the de-facto RISC ABI declared by Motorola & IBM. For this reason, it uses wierd outdated PC-relative addressing of data, completely leaves register r2 (RTOC) ignored, and much more oddities that are not present in other PowerPC ABIs. The usage of MachO saved Apple about 6 months of changing the development tool chain to support more modern ABIs, but at the price of 30-40% performance loss (according to tests made by some kernel/lowlevel AppleComputer engineers I know). --SlavaKarpenko

Is it the Mach kernel's fault or the MachO format? --AdamAtlas

I can't believe Apple is not addressing this.  A 30-40% performance boost for OS X would make the difference in usability, IMO.

That's ridiculous.  Sure, it saved them six months or so, but wouldn't six months be worth a boost of that level?  I thought Apple was devoted to quality rather than just spitting out OS after OS (like some other company I know of)!  Apparently, I have put too much faith in them! --J. McArthur

You can lose a lot of ground in six months. -- RobRix

For the hard facts (vs secondhand hearsay) on MachO vs CFM, take a look at http://www.omnigroup.com/mailman/archive/macosx-talk/2002-October/000309.html - ignoring r2 may not be such a bad move.

In the current Mach-O ABI, r2 is not unused, it's available as an additional volatile register. (This *is* a change from what Apple originally did -- GCC 2.95.2 ignored it entirely, because there were plans to use it for various things, but nothing ever came of that. Apple GCC 3.1 adds it to the list of available volatile registers.)

