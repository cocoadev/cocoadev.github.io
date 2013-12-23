---
layout: page
title: TigerAndPantherCrossDevProblems
---



I'm having trouble with an app I made running on Panther, after I compiled it in General/XCode 2 on Tiger. People are emailing me, telling me that the window won't open, yet the Help docs and Font panel will. It's a document-based app, and contains references to General/QTKit, but that's supposed to work in 10.3.9... any ideas?

----

1 - Have them check their console (logs) for any messages from your app and post them here; we have no idea whatsoever what the problem is if you don't give us something other than "it's broke" to go by.

2 - Are you *sure* they're running 10.3.9. Better yet, do you have code to verify this, exiting gracefully (or disabling the affected portion of your app) if the conditions aren't met?

----

What is the proper way to disable parts of the code in earlier versions? 

*Ask the system (look for "gestalt" in the ADC Reference Library) for its version. If the version is insufficient, you can avoid problems by simply not invoking code that creates or tries to use objects that require a higher version. If you have a more specific question, search all the resources available to you first and if you don't find an answer, start a new page.*

----

Compile with the Panther SDK if that's your minimum requirement. Then you can at least check to see if there are API problems.
