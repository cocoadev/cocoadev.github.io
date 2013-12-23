---
layout: page
title: GeneratingHtmlDocs
---

**Note:** The name of this page is misleading, as AutoDoc can generate more than just HTML. Sorry, my bad. -- MichaelMcCracken
----
Answering the question: * Anybody know the best way to build generated documentation automatically from Objective-C source files? *  

* Check out AutoDoc which can be found at stepwise, or as part of the MiscKit - * http://softrak.stepwise.com/display?pkg=2163&os=20

Also, check out Apple's OpenSource HeaderDoc - http://developer.apple.com/darwin/projects/headerdoc/

----

**Automatic API Documentation Generation** 

It is really easy to set up a documentation build for a PB project with either AutoDoc or HeaderDoc: 


*1. set up a new target "Documentation" (or whatever) 

*2. Create a new shell script build phase (Edit target "Documentation", click on the files & build phases tab, go to the Project->New Build Phase menu) for:
 * HeaderDoc: each of the two perl scripts that comprise headerdoc (headerdoc2html.pl and gatherheaderdoc.pl), with appropriate options
* AutoDoc: the autodoc executable, with appropriate options


And now you can build API docs automatically from within PB.

----

Also check out the article at MacDevCenter (http://www.macdevcenter.com/pub/a/mac/2004/08/27/cocoa.html) about free documentation tools & Xcode.

--TheoHultberg/Iconara

