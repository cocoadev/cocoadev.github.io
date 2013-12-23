---
layout: page
title: XCodeVersionOneDotOh
---



[Topic]

----

Just now it happend to me, that General/XCode crashed and an General/AppleScript, I was developing 4 full days was deleted !!!!
I did not copy, that was my mistake. First I had so many problems with your shit General/AppleScript Studio: So many functions, that
are documentated, doesn't work. And now, everything is deleted..
Thank you all, developers of General/XCode, you are my heros for today !!!! I can't grasp.... **<-- Uhhhhhhh... Xcode is written by Apple Computer Inc., not the General/CocoaDev elite.**

----

I wouldn't mind seeing updated General/DevTools for Jaguar before Panther's release, but as far as Xcode goes I'll probably wait till I've got Panther in my (sweaty little) hands. But if people who have it could provide commentary, that'd be swell!

Xcode will be available for Jaguar, for free --General/ThomasCastiglione

In the words of some contemporaries of mine: suhweet! -- General/RobRix
----

Where did you hear that Xcode would be available for Jag? Where can I get it?

----

Hi! I installed Panther and tryed General/XCode.... unfortunatly it's too unstable (at least for me).
I managed to crash General/XCode 2 times in 3 minutes!!! First I changed preferences a little and kaboom!!! Unexpected blabla... ok... I relaunched the app and opened my current project: Then I tryed to change the code editor mode(toolbar icon) and opened another file and kaboom again.... XCODE CRASHED!!! And that was enough for me.

It seems to me that they released General/XCode in a hurry without beta testing OR they think it's faster/better/cheaper for them that the developers beta test General/XCode.

-- JP

----

That's what a developer preview is for, isn't it? To preview to developers, the people who can live with instability, so you can get your product up to speed while they do the same with theirs?

Sure, but they could have fixed obvious bugs!

They probably did, I guess. I don't use software that crashes very much, and I don't make software I don't use-- I wouldn't be surprised if Apple's engineers had a similar methodology. So either it's something that didn't show up on their systems, or it did and they just worked around it temporarily or something.

----

Some interesting screenshots:

http://funmac.com/showthread.php?threadid=2058

[Link currently doesn't seem to work - maybe removed at Apple's request? General/RickInnis]

----

Am I the only one who thinks General/XCode seems heavily inspired by Eclipse?  Is it merely coincidence that Apple switches to an IBM chip and simultaneously introduces an IDE similar to IBM's?  Probably just me being weird though.  --Owen Anderson

Having played with neither Xcode nor Eclipse, I can't say, but Eclipse sounds cool enough for this to be a Good Thing.
----
Can someone give some examples as to how General/XCode is similar to Eclipse?
----
Anyway I really hope General/ProjectBuilder != General/LegacyCode :( -- JP

Why? General/XCode is just General/ProjectBuilder + improvements and a new name. - General/ThomasCastiglione

Nope, It looks different and I haven't figured out if there is a  "1 window for 1 project" option. -- JP

It looks a little bit different, yes. The overwhelming majority of it, however, is the same. Even a few minutes casual use will reveal that.. - General/ThomasCastiglione

----

----

A painless transition!?!?!? Ahhhh!!!!  Where did CVS go!?  How can I renable it?  What if I dont' WANT to use SCM, and cough up 600 clams a user!?  Sheeesh!  CVS isn't in the prefs, either, and projects already under CVS control don't show up with any CVS integration once opened with Xcode?  Is this the way it's going to be, or am I just not seeing how to enable CVS on this thing?

Calm down, take a deep breath.  "SCM" is just an acronym for "source code management", it's not an extra product you have to buy.  You can use CVS if you still want.

---

I so wish I could calm down!!!  I can't!  General/XCode is refusing to integrate with CVS... and there is a version control program out there called SCM... So, now that I'm a little calmer... (you can't keep these high levels of adrenaline up forever, you know), does anybody know why Xcode doesn't want to recognize my CVS repository?  Does Xcode not like SSH, or something? Thanks!

I seem to recall that PB doesn't support SSH CVS repositories either automatically. You had to do something to get it to work, maybe edit preferences or something...ah, here we go, try this: http://voronoi.ics.uci.edu/~mshafae/cgl/info/projectbuilder-cvs-ssh.html, or this: http://www.museworld.com/archives/000070.html. -- General/DustinVoss

---
Solution:
CVS doesn't automatically enable in Xcode.  Here's how to enable it.
- checkout your project from CVS
- open project in Xcode
- select your project(first folder) in the "Groups & File" Panel
- open the info panel (command + i)
- at the bottom select cvs from "SCM System"

-- General/HuaYingLing

---
Problem with solution: The Enable SCM checkbox turns itself off immediately.  The Show SCM window says permission denied; what permission? -- W.

----

Thanks.  I had all that stuff set of General/ProjectBuilder, which works like a charm, unlike some other fancy pants applications... in general, I'm a little disappointed by Xcode... too many buttons, too many windows, too many widgets.  I like simple, conservative use of realestate and unnecessary items that slide away, kind of like PB.  Xcode, and the new Panther find are all about WASTING real estate... not happy... no, not happy :(

----

Not sure why the anger.  From the screenshots it looks much like project builder-- doesn't seem to be wasting much space to me.  On top of that, I heard rumblings that they were working to generalize SCM to support a variety of SCM products beyond CVS (I could be completely wrong here, this is just what I remember)... which would mean that CVS being broken makes sense.   I seriously doubt that they will drop CVS support after spending the last couple releases improving it!   -- General/JayPrince

----

My anon-e-mouse anger isn't too serious; some of the new features are pretty sweet; fix and run, distributed builds and zero link all appeal to me on a major scale.  However, take breakpoints, for example, which should be tied if not to a specific file editor, then at least to a project... or at the very least indicate where the heck those darned breakpoints are in terms of file/project location... they aren't visually tied to anything... they float around in a separate window, which is not only a waste of real estate but adds clutter and confusion.  Complimentary tools are pretty much a must for any serious project and sys admin is the last thing a developer wants to be doing.  Installing General/MySQL, PHP and crap like that on OS X to get something like bugzilla is way too hard; it should be ten minutes of tinkering instead of hours of keyboard banging... in short, sys admin is already bad enough (non-standard) on OS X, and I don't want to have to tinker with CVS more than necessary to get it to work with Xcode... I want to set my env vars and move on... Xcode makes it significantly harder than PB did... which is too bad... I like the Apple motto "It just works".

----

Have you been able to play with the binding feature in the new General/InterfaceBuilder that comes with General/XCode?
That looks fantastic. A lot less code to write

----

There seems to me to be a big speed hit between Project Builder and General/XCode, in just about every area. Some of the new features (i.e. code completion) are so slow as to be unuseable. I remember running General/REALBasic on a Performa 6400 where code completion worked instantly...I realise there are more General/APIs in Cocoa, but then my computer is faster as well, and the code completion is taking several seconds to even come up with the list of options, and it stops reacting to key presses while it thinks.

----

Am I the only one that thinks that General/XCode sucks?(Totally, in it's current state, that why it's called a dev release) (YES, YOU VERY WELL MIGHT BE. No, you're not) Code completion is a joke!  It seems to know nothing about types at all... it is merely lexical... i was trying to get it to complete a [self foo:bar:blah] call which was defined a few lines earlier and it brought in a list of a few dozen Carbon constants!  Then it inserts some weird ellipsis character '...' which it doesn't remove when I hit cmd-B to build so it gets a syntax error.  Let's not even mention that it slows down my typing by a hundred milliseconds per character... I've turned it all off, and wish I hadn't upgraded... PB sucked, but at least it was usable.  C# may be a one-platform java, but at least devstudio kicks ass.

----

Wait, wait, wait! To me it seems you're unfairly reviewing a product that is both pirated and pre-beta.  I haven't used General/XCode and don't plan to till it is final because I have faith that Apple will pull through in the final version and it'll be great then. Wait and see before you condemn it entirely.  -- General/BrianMoore

----

I use Eclipse every day (on Windows XP unfortunately).  I'm not sure about the above comment that says Xcode is like Eclipse.  They don't look remotely similar to me.  Based on my experience with General/ProjectBuilder, it would be a very good thing if Apple tried to pick up some ideas from Eclipse (it rocks!).  My boss, who's an old MS Visual Studio C++ guy says Eclipse is the only IDE he's seen that comes close to VS.  And as far as refactoring support goes, the only IDE better than Eclipse in that department (IMHO) is Idea.  When Apple introduces proper refactoring support, decent code completion and background compilation (sounds like they do a bit in Xcode) then I'll be a much happier Mac developer.  An open tool API wouldn't hurt either... Sundog

----

Anybody know what's up with Xcode placing build dirs in my root dir?

----

If, say I have an html file in my project, and I want to include it in the .app, how do I make sure it gets coppied into there at compile time?

----

Do any of you guys know why Xcode insists on installing into the build "uninstalled products" folder, instead of deploying like PB used to?

----

Somebody commented above that Xcode doesn't know much about types. That's probably because Objective-C doesn't care much about types.

In Java General/IDEs, like IDEA, code completion is based entirely on your declared type. That's a safe assumption in Java because your type can't change. If you say "List someList", then "someList" is a List. It simply can't be anything else. Code completion can skip any methods that List doesn't declare. And the IDE can complain if you try to use a method List doesn't declare.

In Objective-C, saying "General/NSArray *someArray" doesn't guarantee that "someArray" is an General/NSArray. It merely tells the compiler to warn you if "someArray" does anything non-General/NSArray-like. It's perfectly legal to send any message you want to someArray. The IDE certainly shouldn't be limiting your list of possible completions based on type.

As for Xcode's quality, it's not even in beta yet. And did nobody learn their lesson from Safari's repeatedly violated NDA? If you keep violating your NDA and leaking software, Apple will just stop pre-releasing it to developers. Everybody will suffer because some people can't keep their word. General/TerryWilcox

Anyone figured out how the Cross Development General/SDKs work in the WWDC Xcode? Are they missing in that build? If so, anyone played with the Friday build at all?<br />-Ryan
----
There is a option somewhere to choose the os version on which your app will run. I am starting to agree with people who dislike General/XCode... I hope final version will be much better.

----

The new version of General/XCode corresponding with the 7B28 build of Panther has greatly improved Code Sense. For me, it works just as well as the autocompletion in General/RealBasic, and faster. -- General/AdamAtlas

----

Hi - I installed General/XCode on Jaguar and found it way too unstable (much to my dismay). I realize this a preview release, but the crashes and poor performance I experienced leave me totally perplexed: how could Apple expect us to shake this out properly when some of the most basic features are totally dysfunctional? Defeats the purpose of issuing a good preview release...In fact, let me submit the following issues in the hope that these problems are all mine.


*Performance becomes extremely sluggish after extended use. To the point where an application restart is needed. (Actually, this is not too bad considering its beta status) Something must be leaking really bad....
* Inspecting Target properties and settings (group info window) is extremely unreliable. Crashes are very frequent, especially when flipping quickly between the properties tab and the build tab.
*The "view info plist as text file button" doesn't work.
*Unclear where to add a custom plist entry of my own.
*Double clicking a target brings up a separate target window displaying build phases only. Inspecting a target's settings and properties can only be done via "show info" (button, right-click, or menu)
*Now this is really weird: projects converted from previous PB releases retain the same old target settings and build interface (simple, expert views etc...) after double-clicking the target in the Groups window (the "bullseye"s).  This is totally inconsistent with target inspection behavior described above!
*Double click a target to bring up the build phase window (???) Select any phase (let's say sources) then right click to show info. When the info pane comes up the "general" tab is selected with no info to display. Tabbing to either "build" or "rules" crashes.
*Unclear how to add New Build Styles. The option is available in the Groups pane but nothing happens when it is selected
*Unclear what the default Build Style is.
*Better stop while I'm ahead...


Don't get me wrong, I'm excited about the new direction and the new environment has alot of promise. Unless my system's configuration is at fault, my conclusion is that the preview was way too premature. At a minimum, Apple should have seriously documented these known problems in the release notes. I'm looking forward to a more robust version.

Is the Panther version this flaky? How about Panther itself? Thx -- PK
YES! The Panther version is pretty stinkin' flaky. I used to work in General/ProjectBuilder all day with only 5 crashes... but I can hardly work 2 minutes in General/XCode until something breaks or annoys me. E.g. try unchecking a file from your classes group, and tell me where the rest of the files went? BTW, Panther itself seems no worse/better than Jaguar at this point, but I've only been running it for about a week. It's actually has a *very* slight interface speed improvement - JLO

* There's an General/XCode for Jaguar? *

Yeah - it came with the Panther Developer Preview stuff about a month ago. Surprised me too, that's why I jumped on it.  Maybe Xcode on Panther is the way to preview?
----

I just remembered that Apple had already announced a release date for Xcode... They said it'll be out on September 15th. -- General/FinlayDobbie

Woohoo... unless it sucks as much as it seems to, from what I'm reading here. --General/OwenYamauchi

----
 ----
 
Well, it's here. (Forgive any spelling mistakes, please; the text system wants me to use a Brazilian Portuguese spelling dictionary rather than Canadian English... je ne comprends pas!) First impression: *wow*. It's all so awesome and confusing!

Slightly later impression: so *that's* how you get (insert feature here) to work.

Take-away impression: it's as buggy as hell, folks. But wow. When it works, it's awesome.

Anybody have any tips on how to make the bugs go away? <-- If I could, I wouldn't use it...its truly buggy and well, um....not designed with a user in mind.

-- General/RobRix
----
Hmm, Ok Panther is released and comes with Xcode, but what about Xcode for Jaguar?

----
Ohhh. that is BS, I've waited for so long for Xcode, but its only for Panther, what the F^&*......?

----
Tell me about it; I have a seeded version of Xcode I have been using since WWDC and have been holding off on a beta release of a product until Xcode went GM. Now it looks like I will either have to upgrade my development machine to Panther or move my project back to Project Builder... Suck...
----
Why does Apple always make you pay $130 for a new OS, its gotten to the point where I miss the good old days of OS 7...

----
What makes you say "not designed with a user in mind"? And how does it compare to General/ProjectBuilder. Just curious. 

----

I dunno who wrote that 'not designed with a user in mind' comment, but... there's no real comparison between Xcode and PB. Personally, I think the smart groups are just grand. And it doesn't make you give up the hierarchical organization like you could do in PB. It's quite humane, and takes up less screen space.

Now, a couple of things--the run window, for instance--don't seem to have been very well thought out. And the groups drawer on editor windows is just messed. But you don't even need it.

You can have single-window mode like in PB if you want, and it's pretty easy; basically just click a button. Me, I prefer one project window and one (or two) code window(s).

The code completion would stall Xcode any time I paused while typing a symbol, so I turned it off. I'd only used it for an hour or two but it's notable that I missed it... it's like the code completion in General/RealBasic where you just hit tab to finish the symbol you're typing. Which is nice. But the stalls were unbearable. Oh, and if the symbol it's completing is a method, it adds ], or at least it does that sometimes. Takes some getting used to.

**You can turn off automatic code completion, but you can still use it at will by typing *Ctrl-.* or *F5* and it should work out the same. �General/DustinVoss**

One weird bug is that if I have a syntax error in a .h file, then I will get a compile error in the first .m file that imports it, as you'd expect. But fixing it, saving, and building again isn't enough... it brings up the error again with *every other* .m file that imports it, even though it's already fixed. Just hitting command-B again (and again and again) will get through it, but it's weird.

All in all, if it weren't so buggy it'd be perfect. I figure they'll get the kinks worked out before long, though, since presumably they're using it themselves. I'm not going to recommend you upgrade or don't since apparently you don't have a choice!

-- General/RobRix (who feels that Xcode is the best part of Panther, but understands why [many] others might disagree.)

----

Whoever asked why Apple always makes us pay $130 to upgrade needs to understand how lucky we are. Windows XP Pro which is the closest Microsoft has come to emulating OS X's features costs $199.00 for the upgrade (if you qualify) and $299.00 for full version.

----

Don't forget that OS X users who upgrade will keep getting new features while Windows users have to wait until 2006 to get any new features. Plus Apple will be getting tons of feedback about released features and what directions they should head. Microsoft has to rely on their faulty imagination and planning.
----
Well.... Xcode is **available** to users... But for 10.3 Panther.

----
I haven't loaded or used it yet, but I note that my retail copy does include packages labeled 10.1 and 10.2 as well as 10.3, but this may just mean that we can build projects for those versions, but need Panther to do it.

----
That is correct...
---- Xcode is the best dev app ever, the compile speed is Warp 9.7. Go Voyager,... Oh sorry, I love Xcode.

----

I saw an General/XCode plugin (some kind of snipplet menu thingie), and I wondered how one makes plugins for General/XCode. Does anyone know of any Plugin SDK, and where to find documentation? A quick googling didn't give any result.

-- Theo Hultberg/Iconara

To start a new Xcode plugin:

File / New Project / General/AppleScript Xcode Plugin

For Documentation:

File / Open Dictionary / Xcode

Have Fun...

Great, thanks, didn't realize it was applescript... --General/TheoHultberg/Iconara

*You may not need to write a complete plugin - Xcode provides a lot of functionality to do plugin-type stuff with shell/perl scripts.* [http://developer.apple.com/documentation/General/DeveloperTools/Conceptual/General/CustomizingXcode/Concepts/Scripts.html]
----
How do I get access to the Xcode toolbar images, they are not in the bundle.

*It looks like they're at /System/Library/General/PrivateFrameworks/General/DevToolsInterface.framework/Resources*


----

Aside from collaborating/sharing with PB-only partners, are there any occasions when one
ought not to upgrade a project to Xcode?

*If you're using Panther, you don't really have a choice - PB isn't a supported option under 10.3, and Xcode isn't supported under 10.2. If you're working with 10.2/PB-using colleagues, make sure you install the 10.2 SDK when installing Xcode (I don't think it comes as part of the default install) and keep using legacy (non-native) targets.* 

That's not true -- General/ProjectBuilder is still available and runs under Panther.  I'd still recommend using General/XCode and legacy targets though.
