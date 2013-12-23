---
layout: page
title: ObjectiveCeeRefactoringProject
---

I'd like to propose putting together an open source refactoring framework as a ProjectBuilder plug-in. It'd be nice to (and we might) have access to an Objective-C parser (Afterall, Interface Builder obviously has one), and if that was available perhaps many of the concepts of the GPL Transmogrify plugin for Java could be applied to this project. I've come to depend on these kinds of tools at work (I use IntelliJ Idea for Java coding) and I'd love to have them for my Obj-C stuff at home. I might be willing to lead the project if others can volunteer some expertise. - WilliamBajzek

----

Hmm, if we can't get access to an Objective-C parser, we'd need to write one ourselves (obviously). This would be a highly useful project, I can think of maybe a half-dozen things I'd like to do that would make use of it (and each of them would extend the Objective-C language... SmallTalk-80-style blocks, for instance). Perhaps we should post to the ProjectBuilder-Users list and ask if they could make their parser available, and if not, go ahead and write one ourselves. -- RobRix

----

I could be wrong, but I assume it's probably available with the gcc source code. I don't know anything about parser generators, although I've done plenty of homegrown parsing in perl and python. I would like to have this be pluggable, so that the same plugin could potentially be used to refactor C++ or Java stuff within Project Builder as well. Also, it'd be good to come up with a good format for defining the refactoring procedures possibly outside of the code in order to make it easily extensible... Perhaps using embedded F-Scripts. - WilliamBajzek

----

I know a little bit about parser generators, having tried rather desperately to figure out how to get ANTLR ( http://www.antlr.org/ ) to do what I want it to do for the past few months. I think that on the whole, I'd rather have an Objective-C one just because I *know* Obj-C. I agree that a refactoring device would ideally be flexible enough to take on other languages eventually (especially since ProjectBuilder's great for editing just about everything?PHP refactoring, anyone?), I was just commenting on wanting the parser for other things as well.

So, I guess a refactoring plugin would have to be pretty skeletal on its own. It'd provide some controller code, and then each of its plugins would provide a parser and the refactoring logic? Maybe we could centralize some of the refactoring logic (in terms of design patterns, I guess) in the PB plugin, I'm not sure. I don't have any experience at all with any kind of refactoring other than the manual kind. -- RobRix

----

I'd like to keep pluggability in mind, but definitely start with an Objective-C implementation (in fact, I personally am not interested in working on other implementations, but I wouldn't want to preclude them). So, just running through some ideas here, we need:


*To know how to make a ProjectBuilder plugin. I don't remember seeing documentation on Apple's site for this, but some people have been able to figure it out somehow. Perhaps there is some legacy NeXT documentation laying around that would include it... Alternatively, we could consider building a separate application that can provide services to PB.
*To know how to use a parser generator or find one that will provide us output we can work with.
*To figure out how to actually do the refactorings... Possibly using an embedded scripting format. the Transmogrify http://transmogrify.sourceforge.net thing appears to use some kind of XML file, but I haven't looked at it closely.


Feel free to add more. This sounds like a lot of fun... Although I should mention that until my monitor returns from AppleCare, I probably won't get a chance to do much on it... Hopefully next week. - WilliamBajzek

I just looked at some of the Transmogrify source, and determined that if we're going to attempt this project, we will write comments in our code :)

----

ProjectBuilderPlugins are certainly possible, but the API isn't public. Perhaps we should ask the guys who did the CompletionDictionary? ( http://www.obdev.at/ ) The aforementioned ANTLR parser generator isn't particularly easy to use, but it's better than yacc/lex and their variants. It generates a Java or C++ parser... bit of a problem there, but I guess we could either port the code to ObjC or use it as a subprocess (think NSTask). Probably the former would be better because we'll need to have a lot of interaction with the parser. Again, refactorings aren't my specialty at all, so I'll leave that part to you.

As far as a project goes, what license would we want to do it under? Easy to say "GPL", but I for one would feel more comfortable with something like BSD. Personal preference, in part, and also the ability to reuse code I've made in projects I don't want to open. Something to think about, anyhow.

I'd be willing to put work into the ObjC parser. I'm working with ANTLR just trying to learn it, but I think eventually I might like to devote energy to writing one more manually partly so I get a better understanding of how it works and partly so I can have it in ObjC :)

And regarding commenting source... well, yes :) -- RobRix

----

I'm not terribly familiar with those licenses. I see no reason for this to not be completely open, but we could start work 'in house' before deciding.

Writing a parser is fine, but I'd like to investigate the possibility of using the parser code that gcc uses if possible... simple reason is that it's guaranteed to be syntax compatible. Of course, as I said I know nothing about the parser generators so I don't know how useful its output would be to us. 

I'm also pretty keen on trying to use embedded F-Script to write the refactorings... It may be possible to base some of that code on the Smalltalk refactoring browser, and would make it easy for anyone to write/debug/test refactorings without needing to recompile the plug-in. Also, I haven't looked at the F-Script code yet, but there may be something we can learn from that as well.

I'll play around with some design ideas this weekend and make a page to discuss it... Feel free to do so as well and we can compare notes. - WilliamBajzek

----

To my knowledge, the BSD license is pretty much just "the rights belong to so and so, do what you want with it," whereas GPL forces the source to stay open no matter what... which is why it's been called viral. However, we'd probably take a slightly more in-depth look at it before deciding :) And yeah, there's no problem with starting private development prior to figuring out the license.

At the very least, the initial parser would be able to use the gcc code internally (maybe with ObjC++? I dunno if gcc is C or C++) and then I could move it towards Objective-C or just replace sections bit by bit. This might be a nice thing to have gcc3 for: faster C++ and ObjC++ compile times. I'll download the gcc2 source and take a gander.

I think using F-Script is a great idea, both because you won't have to recompile, but also because it'll make it easier for users (well, technically the users are developers) to add refactorings if they want to.

I'll be sure to post my thoughts on here somewhere, but for now, to bed I am a-heading. -- RobRix

----

Okay, well, I took a look at the gcc source. As expected, the C and Obj-C parsers are written in yacc, and that's white noise to me. Unless someone smarter than me comes along and does something with it, I'm not sure I can do much (if anything) with it.

The good news is that ANTLR has a GNU C grammar as one of the examples. It seems like I can use it as the basis for an Obj-C grammar and then we can either port the parser or use it as a subtask.

I've contacted the author of the GNU C grammar (Monty Zukowski) to see if he had any comments or whatever, so hopefully I'll hear something soon. In the meantime, I'm going to see if I can learn more about ANTLR and Java (never thought I'd find a reason to learn it...). -- RobRix

----

Idle thought: perhaps the parser really should be split into another page so it's not cluttering the refactoring notes... -- RobRix

----

I just want to see if my understanding of this is correct... Could you just use yacc combined with the obj-c grammar to spit out a parser in C that we could then wrap in an object for usage? 

----
So, a simplified scenario to get us started:


* Bob selects a block of code
* Bob triggers a refactoring in our plugin
* Plugin asks the parser to return a representation of that block of code with which to work.
* Parser attempts to parse the code(*). If it fails, it throws an exception and Plugin reports it to the user.
* If it was successful, Parser returns a parse tree to Plugin.
* Plugin gives the parse tree to the refactory
* Refactory performs the refactoring(**)
* Refactory returns the refactored parse tree to Plugin
* Plugin replaces the old code with the new code.

* the parser probably needs to parse the entire block, and in fact we might need to be sure that the entire project can compile first, which would guarantee that our parser could give us a good tree.

** During the refactoring and elsewhere in the process, there will usually need to be dialog with the user to figure out exactly what to do.

----

If you have fink, you can install class_dump, which was written by one of the OmniGroup guys and can be useful in analyzing existing plugins as well as ProjectBuilder itself in order to figure out how to implement this. The service idea is still a reasonable backup; it would allow the program to work with (say) CodeWarrior or BBEdit if someone so desires. For the time being, we can get a pretty good start by writing it as a standalone app, and make a basic assumption like all code that might need to be touched would be contained within one top-level directory. The other details could be worked out later. My wife works for Apple, so I'll ask her to find out if API documentation for making PB plugins is legally obtainable (It may just be a matter of asking). 

If you get a chance, try getting an eval key for IntelliJ Idea, grab some open source Java app, and play around with their refactoring tools. They'll give you a good feel for how these things can work. 

-- WilliamBajzek

----

FYI, I haven't given up on this project, I've just been too busy to devote time to it. Sorry. 

-- WilliamBajzek

Likewise, and unfortunately I've had my mind thrown out of code mode. So I'm designing stuff and all (GraphPaper is your friend!). But I haven't forgotten about the ObjCParser stuff, no worries. Ohhhh... and I got *really* confused about the yacc/lex thing... I think we can just go ahead and use the ObjC parser files and then wrap that. Once I figure out how to use yacc correctly. *sigh* -- RobRix

----
I've been playing some more, and now have a plugin that allows block selection on a block-by-block basis.  It's still not plug-into-able (though it will be), and doesn't do anything beyond playing with the selection yet, but it sort of works.  If nothing else, it shows how to plug into PBX.  Needs OmniFoundation. http://www.tufty.co.uk/Software/Refactor.dmg.gz

I'll keep playing with it, let you all know how I get on.  Next up is identifying 'similar' blocks to the current selection.

Oh, and I removed my braindump from this page.  If anyone's interested, either use the version history or download the plugin (It's all there in the source folder)

TufTy
----
TufTy - I just tried your Refactory plugin with the April dev tools and 10.1.5, but I didn't see any difference (ie, no menu and no extra window popping up...)
Any ideas what went wrong?

**Answer:** I thought I'd leave the question up to inform others who'd like to try it: it turns out the problem was that I was using a version of OmniBase & OmniFoundation that were linked to use the @executable_path/../Frameworks/ directory, but when you use those frameworks within a bundle, they fail to link at bundle loading time because the @executable_path is /Developer/Applications/Project\ Builder. (they need to make this less of a hack, or at least add a @bundle_path hack too...)

I also noticed that the code block selection fails to work correctly on something like this:
    
if(YES){

    NSLog(@"this will fool it - }");
}


This is a good example of why we need to use the real ObjC parser, not RegExps. I suggest that interested parties look into the source for gcc available on apple's website at: http://www.opensource.apple.com/projects/darwin/1.4/source/other/gcc-932.1-6.tar.gz

- that is probably the most robust way to get ObjC/ObjC++ parsing done, since PB's indexing engine (assuming that's what does the syntax coloring) is occasionally wrong, and that's no good for refactoring.

**Update (Wed 08/28 02:39 PM):** I think the scripting language F-Script (http://www.fscript.org/) might have some code that would be very useful for this project, ie, a complete and working Objective-C parser...

I'd love to help out in small ways with this - I wonder if it'd make a successful sourceforge.net project? Does anyone think there are enough interested developers to make a project out of it?

-MichaelMcCracken

Me too! I'd be happy to contribute with what little I can if there was a project set up.

-PerSquare

----

You're absolutely right, of course - Regexps are rubbish for this - I've been playing with gcc3 for a while (libcpp looks rather tasty for this project) but haven't spent much time on it recently, been busy frying other fish, as it were - The stuff I pu together does illustrate quite nicely how to put a PBX plugin together though ;-)

If you need help, consider me included - If you set up a sourceforge project please add user tufty to it?

TufTy

----

Is anybody actively working on this? I just discovered IntelliJ IDEA, which has some amazing tools for Java refactoring. I'd love to have something like that for Objective-C. If anybody is already working on such a thing, I'd be happy to pitch in.

EricWang

----

It seems that we've got a few people who want to help, but nobody who wants to lead. Unfortunately, this is a pretty serious project. I wonder if we offered enough money to Apple, would they hire more PBX developers and give us a nicer PB for pay? I'd pay ~$50 for a PB with even a few of the features of something like Eclipse (eclipse.org)...

- MichaelMcCracken

----

I am posting this anonymously, because I don't want to get people's hopes up prematurely -- but I am actually in the middle of writing a new editor for OS X, and refactoring is planned.

The planned release is in the beginning of August, it will be shareware but around $25. It currently has foldings, macros, syntax highlight, rectangular selection (in a much more flexible way than BBEdit, i.e. drag it around, pipe it through a Unix command etc.) and a lot more (so no, it's not just a pretty GUI on-top of NSTextView with search'n'replace :-) ).

Unfortunately Project Builders support for external editors is basically limited to Carbon Applications (since the Apple Events sent cannot easily be captured from a Cocoa app), furthermore jumping to a given line seems to be hardcoded for BBEdit (using its AppleScript interface) -- I have approached the PBX team with these concerns, but to put it simple, they have ignored me.

So I am thinking more and more of providing a full IDE (since PB is also "just" a wrapper for GCC/JAM and IB), but this of course will take longer time...

But back to refactoring: I have never used IDEA nor Eclipse myself (though I do know of the programs), so refactoring still hasn't gotten part of my normal work-flow, therefor it would help me a lot if people could list which refactoring patterns they use the most and/or what they would like to have for Objective C.

----

I'm also working on an IDE based on Smalltalk's Browser object.  I don't have anything relating to refactoring yet.  I'm approaching the problem from a different angle.  See ObjCBrowser. --JoeOsborn

----

Somewhere on my PC I have some Squeak code that actually reads a grammar file and produces....I can't remember! I do remember running it against a C grammar. I'll dig it out and see if it could be of any use somewhere. I love Smalltalk!

Also, while a refactoring project is a noble idea, I think that we have the opportunity to do something truly magnificent if we treat that as the secondary goal. The primary goal being this (and I can't imagine why compiler writers don't out this in for us anyway seeing as how they've done all the hard work in the first place), the idea:

let's hammer / tweak /. persuade GCC to accept a new command line option along the lines of 'assemble and stop' such that it parses the file, produces the interal parse tree and then does a complete walk of that tree throwing out a structured XML document containing everything you wanted to know about that compilation unit: externs, dekkuls, functions, implementations...everything.

Once we have the guts of it encapsulated as XML then you can write a million tools all feeding off of the XML.

Finally, another option....tell GCC to compile an XML file instead of a source file! IT WOULD HAVE TO BE FASTER I would imagine as the XML contains the syntactic structure of the code to a much higher degree than flat ASCII does.

That's my two pence.

Sean Charles

----

A noble idea, but it sounds like something that's far far beyond the capabilities of most of us hanging around here.  Something like that would probably take years of volunteer time, or several well-paid code monkeys working full time.  And anyways, does anyone around here really have a thorough enough understanding of the inner-working of GCC to attempt it?  I hope someone proves me wrong, but I'm afraid that this is all too likely impossible.  --OwenAnderson

----

I'm not convinced! HA HA HA. I have been software engineering for about eighteen years. I have done a fair bit with XML over the years in various languages and I once got involved in writing a logic compiler for British Rail (how long ago) for compiling track/signal interchanges into a ROM table blah blah effing blah.

The GCC source code is the key....OK, I am sure it is *big* but that's all relative and besides, we don't want to actually do anything other than add some code that steps in once all the parsing is done it should be *relatively* simple to:

  (i) locate the parse tree
 (ii) locate the symbol table
(iii) locate anything else we want

and then smack it all out wrapped in lovel XML. That's 'stage one'. Stage two is, as the Haynes manuals tell us, the reverse of stage one, minus the oil and grease you get in real life.

I am not personally scared to tackle any of this but for the following small detail: If it wasn't for the fact that I am currently unemplyed and on the verge of losing the house and my life in general then I would love to get stuck in. maybe when we ar ein the council house I will have more time....I will never sell the PowerBook!

Sean Charles

PS: Anybody got a job going for a good A/P?

----

You are a brave, brave man, and I wish you luck.  I don't know the first thing about compiler writing, but if you ever need a random code monkey to help, send me an email.  --OwenAnderson

----

I'd like to report from an experiment of storing the parse-tree in an XML-File. At our university we did this experiment with a functional language. For a typical small project the resulting xml-file was around 100 Megabytes large. While we where able to reduce this dramatically to something like 10 Megabytes with compression it's still the reason why nobody around here tries this anymore. --MartinHaecker

----

100 MB??? Could you perhaps elaborate on why it became this large?

If we say 300 lines, then that is 340 KB pr. line in the source program, and if each line cause 10 nodes in the parse tree, then that is 34 KB pr. node in the parse tree (represented as XML)... these figures sounds like they are off by a factor of thousand or so... :-)

----

Uhm, sorry for the misstatement. Of course I meant for a small project (something below 10.000 Lines) we got this result. Also of course the parse-tree was complete, with all the meta-information that the compiler had computed at this point. I gather that that much information won't be neccessary for the task at hand, but my point is that xml might not be the ideal representation for this kind of data.

----

There is a command line utility call tops (try "man tops") which has some powerful refactoring functionality.  Obviously, the command line is a relatively poor refactoring interface, but that doesn't mean this should be overlooked. I think a reasonable avenue of approach is a tiny pbxplugin to trap selected methods, file lists and operations in the (PB)Xcode interface and pump it throught this little gem.  I have built pxbplugins before. I released one called PBXtra http://culater.net/osd/PBXtra/PBXtra.html a while back and the source to that should be a reasonable example.  This was the first thing I every wrote in Cocoa, so don't judge my coding by this horrendous thing - it will however illustrate the basic concepts required to get this refactoring thing off to a start.  I haven't actually tried this in Xcode - but I imagine it works similarly.  If the plugin mechanism is non-functional, you can use SIMBL http://culater.net/osd/SIMBL/SIMBL.html to invade Xcode with your own bundle of code. --MikeSolomon

----

*tell GCC to compile an XML file instead of a source file*

Which is possible by using http://www.gccxml.org/

----

I would like to turn all of your attention to this page https://netfiles.uiuc.edu/garrido/www/CRefactory.html because they are building a Refactoring Metamodel for C that includes the complete Preprocessor and tries to solve its problems.

I do think that it would be a great start to add to this program instead of rebuilding it from the ground up. -- MartinHaecker


----

In case anyone finds it useful, there's an ObjC grammar for lex/flex yacc/bison available at http://www.wodeveloper.com/omniLists/macosx-dev/2001/March/msg00807.html

I have not verified if it is correct, but it looks decent enough. -- RobertChin

Also, a new hand written C-based C/ObjC parser was checked into the GCC4.1 trunk recently, don't know if that's useful. --DavidSmith

----

I'd like in on this discussion. I've dealt with AntLR in the past and used it to create a build component that inserts version strings in every java class (Anonymous inners and nested classes). One thing I remember from experience is that parsing/rewriting loses the original formatting. There was an article that dealt with the problem using a token filter rewriter. I haven't read all the comments here but it looks like you may want to consider more than just simple block refactorings but also handle whole file/project refactorings. For that you'd probably need the parser to rewrite the refactored source or something that can match an AST with file range locations. Maybe you'll want to annotate the AST with line ranges? Its been a while since I toyed with AntLR and read up on ASTs so I'll hush now. --CliftonCraig

----

Just read some more of the above comments and one other thing comes to mind. Just because the resulting XML file is huge/verbose doesn't mean it can't be efficient. Efficiency only becomes an issue when XML is materialized/parsed. If the XML is represented/output as a stream of events then one could write a SAX based event handler to receive the XML and transform it back into source. That said, XML confuses the "thing" we're really after... the parsed tree. An AST is likely the better choice. --CliftonCraig

