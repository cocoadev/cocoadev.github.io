---
layout: page
title: UsingDoxygen
---

I would like to hear of any experiences good or bad.  It's been getting a lot of press lately (General/MacDevCenter & Dr. Dobbs to name a few), so I'm curious.

General/JohnMoore

----

Well, I tried using General/HeaderDoc and it was pretty useless (the documentation is incomplete and incorrect and it sometimes seems to do completely incorrect things like randomly cutting off the second half of a method name) then I tried General/AutoDoc and it seemed as though it may have worked, once upon a time, but now doesn't.  The only thing that kept me from jumping to Doxygen (seeing as I had been pleased with it for some C++ stuff, a few years back) was that it didn't used to understand Obj-C.  It turns out that that changed a while ago.

Setting it up was pretty straight forward and using it was pretty simple to get the hang of (it isn't exactly General/JavaDoc but it is a very close second).  Now I use it for all my documentation (in fact making it a build phase for deployment targets using a shell script phase) and haven't looked back or had any problems with it, whatsoever.

I give it ten thumbs up.

--General/JeffDisher

----

I have successfullt used doxygen to generate HTML documentation from the headers of the General/IconaraDOM-framework (the result can be viewed here: http://developer.iconara.net/products/DOM/docs/API/index.html. It's quite easy to set up, even though I think there are too many options (I'm very satisfied with General/JavaDoc's one-size-fits-all attitude). I have also tried to write some XSL to transform doxygen's XML output to something of my own, but I grew a bit tired of it (it was a bit too much, and didn't pay off since doxygen's default HTML is good enough). It's not optimized for General/ObjectiveC, but rumor says that work is being done in that direction.

There is an article on General/MacDevCenter about Xcode and free documentation tools: http://www.macdevcenter.com/pub/a/mac/2004/08/27/cocoa.html, it covers Xcode and doxygen integration.

-- General/TheoHultberg

----

I reluctantly gave up on Doxygen when I started Cocoa programming ( feb 2003 ) because it was my understanding that doxygen at the time didn't support Objective-C. It made me sad, since I LOVE doxygen.

But anyway, it seems that Objective-C is supported these days and I followed the instructions on the Oreilly article and lo : it works as advertised. Even better, errors reported by doxygen showed up in the build output pane and were click-linked to the error in the header file ( well, 99% of the time ).

Personally, I just like Doxygen the best. I like the style of the documentation, I like the syntax, and so on. It's fast, free, supports every language I program in, and works. Can't ask for better. 

Also, it's as useful to yourself as to users of your libraries. I can't tell you how many times I've had to look up some non-trivial aspect of a lib a wrote two years ago and doxygen's connection graphs and other cross-linking saved my butt.

-General/ShamylZakariya

----

A word of warning when using doxygen and General/ObjectiveC: the doxygen lexer chokes on the     @protocol statement ("protocol-declaration-list", like     @class), if it's at the top of your header file, all your methods will come out as "p", kind of annoying I must say, I solved it with some hackish perl which I specified as INPUT_FILTER in the doxygen configuration file.

Apart from that I still think that doxygen works quite good for General/ObjC. I have created quite a lot of custom XSL and CSS to tweak the output (it's a good thing that     xsltproc can handle HTML since doxygen doesn't output proper XHTML and the XML output is way too complex). I still think about writing my own documentation generator for General/ObjC, but I'll do that in my Copious Free Time, I guess.

--General/TheoHultberg/Iconara

----

One of the main problems with using Doxygen for Objective-c is the way that it groups things together when using categories.  There is no easy way to get all categories for a class to appear on the same page, or to have a page with links to all categories.  I work around this by defining groups for each class and including each category in that, but this is not ideal.

One thing I would really like to see is a template (css / html) to produce documentation in the style of the Apple docs as its a format we are all familiar with and I think it works well.  Does anyone have anything to do this?  The author of General/PSMTabBarControl seems to have found a way...perhaps he can share his methodology with the rest of us?

JKP

----

Bad news (said the author of General/PSMTabBarControl)... I just copied the HTML from an existing Apple doc and edited it by hand.  Great looking results, but quite labor intensive.

General/JohnPannell

----

I know I'm a bit late to the party, but I've created a Python script and some XSLT transforms which allow you to convert Doxygen's XML output into something much similar in appearance and organization to Apple's docs. It's called General/DoxyClean, and you can grab it from my site: http://www.mattballdesign.com

It's a work in progress, so it's missing a lot of things, but it's building up slowly, and it's at least a step in the right direction. I welcome any modifications, so feel free to change it up, and please send me the modifications so I can potentially incorporate it back into the main release.

- General/MattBall


----

Happily, the latest version of Doxygen (1.5.6) released on 18 May 2008 handles @protocol statements properly. It's also a universal binary build for Leopard. (The author states it may also work on Tiger, but has not been tested.) Currently, Doxygen does not differentiate between or make any note of methods marked @required and @optional, which is an addition of Objective-C 2.0.

 - General/QuinnTaylor

----

Apple has posted a page on how to use Doxygen with General/XCode 3. It not only produces the Doxygen documentation but creates a General/DocSet and adds it to the General/XCode documentation window. I find this to be the best solution for my needs.

 * http://developer.apple.com/tools/creatingdocsetswithdoxygen.html

Note however, that the script is slightly broken. It attempts to override settings by appending them to the default Doxygen settings file. This doesn't work for certain settings, such as EXTRACT_PRIVATE and EXTRACT_STATIC unless they come in a particular order. You can get around this by editing the doxygen.config file that ends up in your project folder, or you can use a modified version of the script posted at:

 * http://thinkyhead.com/pub/doxygen-xcode.zip

This version modifies the Doxygen settings directly and handles warnings a bit better. Be aware that with the AUTOBRIEF settings enabled the brief summary will include everything from the beginning of your comment up to the first period (.) or blank line.

Read the heading of the script for usage.

Enjoy!

 - Thinkyhead

----

Thinkyhead,

Is it possible to make your script to work with multiple input sources?

I would like to include two source directories, but it fails when I separate them by a space.

doxset INPUT				"$SOURCE_ROOT/../../source $SOURCE_ROOT"

-Paul Solt

----

Here are some blog posts about getting started with Doxygen in General/XCode projects:

http://www.duckrowing.com/2010/03/18/documenting-objective-c-with-doxygen-part-i/

http://www.duckrowing.com/2010/03/18/documenting-objective-c-with-doxygen-part-ii/
