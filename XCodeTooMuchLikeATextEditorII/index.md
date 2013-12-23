---
layout: page
title: XCodeTooMuchLikeATextEditorII
---



*Continued from:* General/XCodeTooMuchLikeATextEditor

----

This may not come to a GUI vs. CLI war on the conceptual level, but rather a "RUN-OF-THE-MILL BAD GUI" vs. "TIME-TESTED, MATURED, 30-YEAR-OLD CLI" war.  It's very easy to create a really bad GUI. On the other hand, the creation of a GUI that beats the hell out of the CLI is very, very, very difficult.

Look at the Finder, which has room for improvement; for some complex tasks or power uses, an interface like a Unix shell is better. Yet for most file management needs of just about anyone except hardcore geeks, the Finder usually suffices. And of course, it has spawned its clones on other GUI platforms. 

----

Why we should accept these assertions when in fact a great number of very intelligent folks have been working on intelligent programming interfaces (like the General/VisualAge project at IBM) that you seem unaware of?

----

A great programmer may still build a horrible GUI. If some people at IBM are building intelligent programming interfaces, how does that make my wish for an intelligent programming interface on the Mac any less relevant?! If General/VisualAge succeeds, then it's proof that it can be done.

What can be great about a GUI is being able to drag and drop, shape, manipulate, change, explore, etc. items that you couldn't easily do otherwise. Before non-destructive, non-linear video editing appeared with desktop video, you'd have to think extremely hard before making a cut, with just about no way to undo your decision. Changes become tedious and costly. 

I think Cocoa programming is still in its "pre-desktop" stage. You have to think and plan pretty hard beforehand, unaided by the IDE, because once you start writing your source code, named your classes, and so on, there's no arbitrary changing them. General/BookBuildingCocoa lists 22 steps for renaming the automatically generated "General/MyDocument" and related classes!!! If someone tells me, "rename your class," I'd like to be able to do it via a dialog box, or maybe in-place, like you rename a file in the Finder! If someone tells me to add code to my class that archives it, I expect that to be something as basic as configuring a few options, like which instance variables I'd want to save (picked from a list), and whether or not to use a General/NSDictionary wrapper, instead of typing out method names with nineteen syllables. Sure, there's a time for that, too. When you develop the bits that make your app unique. But tasks that 99% of all apps will have to do, like opening and saving, should be added to the app via drag-and-drop. I expect this from the company who gave us the Finder, iMovie, iDVD, and General/GarageBand. We don't just need a strap-on GUI. It's pretty hard to write a decent WYSIWYG HTML editor (most suck pretty bad), which respects your hand-written code, while also sparing you the repetitive tasks of manually putting "<p>" and "</p>" around paragraphs. So a decent "WYSIWYG" General/ObjC editor must be even harder to write.

It shouldn't stand between you and your code, unless you invoke it. (But I don't see why anyone would mind things like synchronizing header and .m files, adding method declarations to one and automatically method stubs to the other. Or bringing up separate windows for your methods, instead of scrolling through the entire source code of the class.) �Andr�s
----

These are all valid points. Anything that saves me tedium is probably good. Cocoa, therefore, is the BEST THING IVAR. But my tedium is still way above zero. Probably something to do with environment variables. �Brent

----

In software development it is no less true than elsewhere that you get what you pay for, but sometimes much, much less.
Fortunately, in the case of Xcode, you still get more than you pay for. Even if you pay the 20 bucks so they'll ship you the CD.

Anything truly likely to save you time and tedium is also likely to be considered worth something to you, and you should expect to pay for it.

Couldn't you just solve the General/MyDocument dilemma with a sufficiently-sophisticated General/AppleScript? Isn't Xcode scriptable?

Maybe Xcode should have a for-a-fee version that gives you all those perks without having to lift a finger over a keyboard.

In reality, what you're after leads to the same old bifurcation that follows from "Them that has, gets."

----

Apple would greatly benefit from becoming the best development platform in the world. What if corporations in need of heavy in-house development would choose the Mac over anything else for that purpose? So I think there might be a way to keep it free.

Scriptability is great, but can you script Final Cut Pro and turn it into iMovie? 

These points may be ridiculous for developers who've hand-written millions of lines of code in 20 years and can't imagine a better way... But those who came from the low-end, high-level kind of programming like General/REALbasic or even General/FileMaker, a system that does a better job of holding their hands would be clearly better. And I'm positive that once such a system is around, hardcore pros would embrace it. That is, again, if it's done well.  �- Andr�s

----

General/XCode is not, by far, the top of the line professional environment.  In fact, it's a particularly bad example to pick to get an idea of the capabilities of professional environments.  The fact is that it's only simple, repetitive code that benefits from drag and drop-style programming, and this sort of code isn't what professionals spend their time writing; they write it once, then use it forever, or pay someone to do just that for them.  

The problem is one of semantics: HTML, at least, has visual semantics behind it; it's a display language (at least in usage), so you can go from display to code, the same goes for iMovie, and even General/GarageBand (which has a simple visual domain representation).  What you're asking for here is a visual system which can represent arbitrary semantics. That's harder than you seem to be assuming.  In the end, the vast majority of the code that's the hardest to write would *still* be hand-written; you've gained essentially nothing for a large amount of work (which is why professional tools are so bloody expensive and still don't do all of what you ask).  For a couple of examples, how would you represent mutual recursion graphically? mutual dependance?  And even then, in the end, the code that's the hardest to write is still written by hand: so what's been gained with a fancy IDE that does drag-and-drop document saving?

If you want to convince me that this is even *possible* let alone desirable, you'll have to provide a more convincing example than refactoring classes (which even Eclipse can do easily).

----

The hardest part of movie making is shooting the movie. No software will make your script, direction or acting better. Same with music. Unless you want to make elevator music or music that has been done before a million times, you'll need to work hard on the actual creative process. Nobody will become Beethoven, or even Metallica by combining Apple loops.

All I want is an Xcode that helps me a lot with the simple stuff, so I can focus on the hard-to-do stuff.

You say those assists are little help, maybe saving 5% of your time. That's hardly negligible!!! --Andr�s
----

As I read the posts on this page and the General/NextGenerationTools page, I see some overlap with some Java programming tools, such as General/IntelliJ's IDEA. IDEA is not only a code editor, but also has advanced code-writing templates and "wizards" that implement design patterns, a find system that searches for code interrelationships, code refactoring, and code analysis/quality assurance tools. As much as I love Cocoa and General/XCode / General/InterfaceBuilder, I miss a lot of the programming capabilities I have with IDEA when I'm programming in General/ObjectiveC, and unfortunately this makes programming in General/ObjectiveC relatively more tedious, repetitive and error-prone. IMHO, this goes against the spirit of General/ObjectiveC programming. There is simply no good reason why General/XCode shouldn't include as good, if not better, program design, writing and analysis tools as Java General/IDEs. -- General/ScottSteinman

----

This discussion is basically stuck working out whether or not tools, specifically General/XCode, can be improved to allow better control over software design.

Actual examples have been given of possible improvements. I'm not satisfied, but who is?  I'm not sure I'm trying to convince anyone, exactly, just maybe inspire them. I think there are development tool engineers at Apple with new versions to think about, and I won't mind if they find something to consider in these pages. �*Brent*

----

The most visible result (these days) of making development tools easier to use is the profusion of schlocky $10 shareware apps as
scam artists knock out a dozen pieces of junk a week building chunky ( and clunky ) front ends on UNIX command line tools.
For them, the rate limiting step may actually BE contending with the deficiencies we've been discussing.

A ten-thousand line opus worth its salt has much more important things to worry about, and I don't know how much
more the Apple engineers can help you with that one. Maybe a lot.

At my stage of things, better-automating things like a table data source or (your pet example here, e.g. renaming the document class) would not make that much difference, unless my app had ten tables in it.

Sure, automating those repetitive tasks will be great for the more serious maestros out there too, and will increase productivity, and I'm all for that.

----

Any effort or development at "democratizing" some technology will end up creating a lot of wannabes turning out atrocious stuff. But I guess we have to live with that. Too many self-styled designers are running amuck with typographic monstrosities, but no pro would really like to move back to pre-DTP times. 

----

What you're highlighting is the difference between ease of use of tool and its learning curve, or maybe ease of use and actual productive power � or some pair of intangibles. So, can we not distinguish between what pros need and what newbies need? Newbies should really learn General/AppleScript or General/RealBasic or something, not Cocoa. Andras admits he's new, but I don't think he was saying he wanted more hand holding from the tools. He's just more sensitive to having to do a lot of redundant typing and flipping back and forth between files and scrolling up and down a lot and other annoyances that old pros don't notice as being annoying any longer (I infer from what I'm hearing from y'all). Anyway I'm done here. I will add some concrete ideas to General/CocoaMostWanted under tools. �Brent

----

There's been a very interesting post over on Joel Spolsky's site http://www.joelonsoftware.com/articles/General/APIWar.html where he talks about what makes environments productive. 

General/RealBasic is particularly interesting. After you get over it being based on BASIC, realise RB 5.5 has:


* operator overloading including the equivalent of operator= and the ability to write RHS operators for arithmetic, eg operator_AddRight
* Java-like interfaces
* the equivalent of Objective-C class categories ('extends' on the first param to a method)
* an in-language solution to the perennial problem of how to attach bits of code to event handling per-window without writing new classes all the time (events)
* strict typing required - you do have to declare all variables (although as a frequent Python developer this is no longer the big deal it used to be for me)
* reference-counted memory management (Joel's point about not having to worry about memory management)
* overriding of method lookup via operator_lookup so you can add unknown method handling
* scope control for global constants, functions and variables equivalent to a c++ namespace (but not nesting namespaces)


General/AndyDent

----

I've been programming since the mid-70's and have only seen great strides in user interfaces. Now, the question is, do I use General/XCode to develop SOFTWARE (caps, representing lots of time and effort) or do I use it to develop software (lower case representing small, usually one-shot programs)?

As someone who can write code and hold a conversation at the same time (frankly, once you get the design out of the way, the rest is merely tedious transcription from concept to code), I really don't see the problem. HOWEVER, if you are using General/XCode to develop software (as opposed to SOFTWARE) then you probably are right... it isn't the easiest thing to work with.

iMovie lets you know stuff fast. Few bells and whistles; very user friendly. Final Cut Pro has more features that the average hobbyist has the time for! Think of General/XCode as the Final Cut Pro of the coding world and I think you have a good handle on its design goal. It gives you a lot of freedom - more than what most part-time coders really want.

Apple NEVER aimed General/XCode at the casual programmer - there were far better tools on the marketplace. Instead, they felt the need to share the tools developed with you and me for, unusually, free.

The majority of people who buy Macs have no desire to program. More people are interested in getting a better, faster, operating system (or cooler iApps) than they are in a cooler, better coding environment.

Would another level of abstraction HELP the average developer? Code is "only" the expression of a concept.

General/XCode is great for professional developers. Probably not so great for hobbyists. It is hard to create a tool that satisfies both markets.

--Lloyd--

----

If tedium can be automated away, why not? General/XCode "has" renaming (aka find and replace), but it's caused me no end of pain. Similarly, why should a professional developer ever write a basic accessor method? The vast majority of them have exactly the same simple form, and it's often the case that lots of them have to be written.

----

For professional developers, writing accessor methods may take up only 0.01% of their time, compared with 1% of a hobbyist programmer's time. Professional programmers spend so little of their time actually dealing with the things that can effectively be automated that it's just not worth it.

-creed

----

What is it that a professional does which a hobby programmer doesn't? For me there is really little difference between the jobs I get paid to do and the things I do for myself... though the lack of automated accessor creation in Xcode hasn't really bothered me, a lot of other things have, like lack of macros, clipboard history, column selection, better text transformations etc.

----

Does this mean that a real top professional developer spends just a few hours creating the class structure, and then spends weeks implementing all those classes, never leaving a method for hours at a time? Nobody should be required to use any abstraction that he finds unnecessary. 

Suppose there are graphical clues and smart code management options (like maybe "smart" or "dynamic" archive methods that the system maintains by itself, which remember to include all instance variables, even new ones, if the programmer so desires). If these things existed and benefited newbies, then my guess is that some pros would end up liking them, while some would not. Anything that goes between a developer and his code would obviously need to be optional. I'd imagine such a version of Xcode to have a configuration option (a setup assistant walking you through various "hand holding" options on first launch), and a carefully planned system to handle such things. (Like, what if you open someone else's project that has different "hand holding" options? You could have Xcode warn you about them case-by-case, or it could automatically override settings, or leave them alone, etc.)

--Andr�s

----

A far "bigger" problem for me (at least) is the General/XCode/IB interface which spews windows everywhere - it's OK on a 20" display, perhaps, but eclipse or IDEA or Visual Studio is usable on a 12" i/General/PowerBook-style display. I'd infinitely rather they took cues from Aperture, fixed the windows together (putting the editing windows on freeable tabs), and put interface builder in the same interface.

----

A dominant pattern all through this discussion is that those who have adapted to another IDE are frustrated with some aspects of General/XCode. I, who never had the pleasure of some of these other systems, have never felt much of this frustration. I "get it" that people see ways to make the IDE work better, but remain agnostic about how "bad" the current "defects" really are. I doubt that any of the "defects" are absolute, but only appear relative to another system that someone previously grew to know and love. *Or perhaps some ideal system that will never exist.*

----

Isn't that the ultimate goal of any program though? To be the best tool for its job that it possibly can be? I know when I work on Adium I'm trying to turn it into the best communication program, bar none. Even then, it could still be better, and should be made so. If Xcode can make my life easier, it should. End of story. --General/DavidSmith

----

I'm all for idealism, but this whole series of rants has now gone on for two long pages and consists mainly of people complaining that General/XCode doesn't help them get rid of their dandruff. File for an enhancement at bugreporter.apple.com. End of story. Sure, the goal for a program is to become better, but Apple seems mainly interested in toying with big, flashy new feature sets in the frameworks, and helping developers over the processor transition. Anyone want to chime in with how amazingly splendidly wonderfully THAT's going?

As far as the General/XCode tool itself, they seem to strive mainly to keep it from having so many frustrations that it actually drives developers away. Frankly, I'm pissed off enough at Apple for what they've done with Darwin that I could almost not give a damn if the development tools leave a few things to be desired. No major corporation is ever seriously going to be interested in OS X as an IT solution, with some of the idiocy that goes on under the hood. Apple developers are doomed to that couple of percentage points of the desktop market for the forseeable future. Get exercised about something real, instead of spouting platitudes about idealized toolsets. Adium, for heaven's sake? Does anyone think GE or Citibank is getting interested in Adium yet? General/XCodeTooMuchLikeATextEditor my ass. General/CocoaDev too much like a nursery school sometimes.
----
Who are you and what are you even ranting about ?  What are you pissed off about in Darwin ?  Maybe you should start another page for that.  And since when has kernel and library implementation ever had any relevance to the selection of an IT solution ?  Hello, Windows ?
