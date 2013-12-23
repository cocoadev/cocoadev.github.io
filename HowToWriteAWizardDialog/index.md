---
layout: page
title: HowToWriteAWizardDialog
---

I am a MFC programmer starting to write cocoa code. Can anyone tell me how to write a wizard dialog in cocoa?, like in most software installation, with "Back", "Next", "Finish" buttons.

----

The correct answer to your question is "no." "Wizards" are aptly named. They are subtle, inscrutable, and take perverse delight in being monstrously unhelpful.

Do not write a "wizard" dialog for the Mac. Do not attempt it. Do not contemplate attempting it. Just don't.

There are better ways to do EVERYTHING. Consult the Aqua Human Interface Guidelines for more information, guidance, and inspiration.

----

I'm working on a framework that will assist developers in creating these wizards, aka assistants. It will most likely end up being a standalone application that will load and run wizard modules created by developers. Until then, you can make a window with an NSTabView that you set to display without tabs. You then make each "page", and add Back, Next, and Finish buttons which connect their actions to an object you make to handle that stuff.

----

I'm working on a wizard right now for licensing an application that has 3 paths.  trial  license, permanent license and a request a license.  I did this with 11 separate windows,  wiring up next and back for each scenario manually.  I thought there had to be a better way, but being new to Cocoa I wasn't sure. I have not tried the tab approach, and I can't think how I would change the window size and contents this way. Does it make sense to do it with tabless tabs if I have 3 possible paths and need windows of different sizes?  Would I still need at least 3 separate windows?  One for each entry point?  Any help or examples would be greatly appreciated.

----

  Why not just use one window and a tabless, backgroundless tab view? :-) You can wire the back/next buttons to switch to the correct tab view item.

----

Maybe you can have a look at the Assistant Kit, it is providing some code and methodology to build Setup Assistant similar to the Colorsync Calibration assistant.

The link to this Assistant is available in the ObjectLibrary section of this site.

