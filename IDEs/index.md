---
layout: page
title: IDEs
---



Integrated Development Environment

The collective name for applications like ProjectBuilder, or XCode (Panther/OS X 10.3 - http://www.apple.com/macosx/panther/xcode.html )

----

Best IDE ever: IDEA, but it's Java-only. :-(  -- DustinVoss

I'd like to disagree.  Eclipse beats IDEA any day! --OwenAnderson

And it's not even just Java....

OwenAnderson: **Without starting a flamewar, please elaborate on why Eclipse beats IDEA -- I use neither, but am interested in picking up one, I have heard marvellous things about IDEA, and that Eclipse was basically IDEA minus some features**

*The most common advantage I've seen is that Eclipse is free. I get paid to write Java code, so I use IDEA.*

If you need to do refactoring and code inspection (QA), IDEA's upcoming version outperforms JBuilder, NetBeans and Eclipse. -- ScottSteinman

Speaking of refactoring, does anyone know of a tool to refactor ObjC? I took a look at the ObjectiveCeeRefactoringProject, but that page no longer seems active. --EricWang

Comparison between Xcode and IDEA (July 29, 2003):   http://glaforge.free.fr/weblog/index.php?itemid=30
It is very biased towards IDEA (Java).

I've been switching Java IDEs quite often... and I've come to the conclusion that IDEA is simply the best for actual code writing.  Eclipse is very nice.  The fact that it is open source and the many available plugins make it the 2nd best IDE out there.  But doing actual work I constantly encountered minor bugs and annoyances and often missed useful features from IDEA.  IDEA of course has its own ideosyncrasies, but its editing, refactoring, debugging (esp. together with JProfiler) make it the better choice, unless you need to use some more obscure plugins/tools(like apsectj etc), then going with eclipse might be the right thing. -- StefanPlantikow

----

Eclipse:  http://www.eclipse.org/
Eclipse  plug-ins:  http://eclipse-plugins.2y.net/eclipse/

----

Active Developer  [http://www.interactive-technology.com/]
Version 2.15 --- September 29, 2003

Has anyone tried this?

Looked super-unwieldy.

----

I just tried the demo version - it looks really cool, with the ability to browse a running app like the Smalltalk Browser, (ObjCBrowser does this with code), and FScript. Then you can edit and reload the classes into the running app, including adding categories and whole new classes. I am really impressed with what it is able to do, and I think one thing explaining its low profile is a confusing website. If they got someone to rewrite their site to make clearer claims, and to look more professional,  it'd be a lot more attractive. Also, the GUI is a little itchy, but I think I'd get used to it, especially for just-in-time compilation. (And it looks like it could support automatic refactorings, but I don't know if that's on their plate...)

-- MichaelMcCracken

----

Anyone knows how to do cocoa development in eclipse?? Where do I find the application and foundation classes???

Thanks a bunch!

----

I'd definitely have to say that Visual Studio .NET 2003 is THE best IDE for any platform! ;)

To quote Bobbins: *That's my favourite book! Apart from every other one I ever read.*

----

I end up having to use whatever IDE is the 'defacto standard' for the client I'm with. This changes regularly. I've used Eclipse, IDEA, and Visual Studio (Both 2003 and 2005). I use XCode for my Objective-C stuff.

IDEA is by far the best. 

Eclipse isn't bad at all for being freeware, although I really hate the 'perspectives' concept.

XCode is third, due to it's lack of refactoring support. On the plus side, I really do love how customizable it is. You can define file types, compilers for those file types, custom build phases, etc.

On TestKit's deployment build phase, I have the custom build scripts do EVERYTHING. They compile the product, generate the HeaderDoc documentation, create the install package and the .dmg image. 

Visual Studio is dead last. For a Windows app, it's ability to navigate around the 'Solution' without keyboard shortcuts absolutely sucks. I can't even easily move back and forth between source files without grabbing for the %@#$ mouse. There is code completion, but the refactoring support is severely limited. And of course, customizing the build is out of the question.

Don't even get me started with the half-assed code generation VS does. Or their APIs. A C# List type is only equal to itself, not even another List of exactly the same length with exactly the same members in exactly the same order. Had to write that one myself.

TimHart

*Shocking - a pointer to list is only equal to another pointer that points to the same place.....*
----
I'm not sure if that was intended sarcastically or seriously. Consider that **many** classes define a sense of equality that goes far above simple pointer comparison. I've had to deal with several classes where equality was best left to pointer comparison, but a list????

TimHart

----

I'm finding that for my needs, combining Xcode's build system (and when needed, target editing) with TextMate's code-editing and project drawer is ideal. -- RobRix

----

Tried all the Java IDEs until I found IDEA. Far and away the best. Our shop is mostly Eclipse. I used to use it. But whenever I respond to the "Eclipse is so much better than IDEA ..." troll with "Oh really? Why?" the first thing out of the Eclipse user's mouth is "Its free". And that is seriously it's best feature. When I buy hand-tools I don't shop for the lowest priced item. So why would I do it when deciding the tool I use to generate my sole source of income.

If Apple simply plagiarised IDEA 7 for Objective-C I would be a very happy Objective-C developer.  Unfortunately even XCode3 falls well short of IDEA in more than just refactoring support. I'd be happy with creating a method implementation from its interface or, better yet, offering to create the method interface and an empty implementation if the statically typed receiver does not respond to the method you have just tried to invoke in your test case or other usage. The biggest impediment to good IDE support for Objective-C is the language itself and its C inherited looseness.  But when you have statically typed variables I don't see why XCode doesn't make use of the information to offer this kind of support.

I write Objective-C because I like Cocoa. Unfortunately XCode doesn't live up to Cocoa's standard and its deficiencies make my Java day job more enjoyable sometimes.

