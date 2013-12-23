---
layout: page
title: FixAndContinue
---



file:///Developer/Documentation/DeveloperTools/Conceptual/XcodeQuickTour/qt_fix/index.html

----

I can't get FixAndContinue in XCode to work. I run the program in the debugger, make a change to a file, and save it, but the icon is disabled. What am I doing wrong? -- AdamAtlas

Never mind, it needs to be a new project, I was trying it on an older project that I updated. -- AdamAtlas

So, how did that work?

It works perfectly over here.

----

Yeah, I can't get this to work at all. I don't want to have to create a new project because the one I converted is huge... however I'd still like to be able to take advantage of Fix and Continue. Sounds really nifty. :-)

Any suggestions? -- MarcWeil

----

I've heard that the projects can't be converted; PB projects are based on jam ("just another make", from the makers of Perforce). XCode projects are a "native" format, which is something internal, probably more like CodeWarrior's project files. You can only do FixAndContinue in native projects. So I think you'll have to recreate the project from scratch.

----

Or... you could read the docs and find out that you just have to update the target....

**You make it sound so simple!**

.... it is simple ;-)  - PtxMac

It's definitely simple. Click your target, project menu - > upgrade target. You are done.

