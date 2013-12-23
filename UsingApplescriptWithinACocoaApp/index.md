---
layout: page
title: UsingApplescriptWithinACocoaApp
---

Ok, Here is my delemma. I have a workflow that uses Applescripts that drive events to many graphic applications (Photoshop, Indesign, Acrobat, etc..). These events are all housed in an applscript that I call the master (which is in turned called by a java app). That all works great, but I wanted to create a custom cocoa dialog box to interface with the user after calling a certain applescript. I created an app in cocoa that asks a question, sort of like a display dialog question, but more involved. This app (when initialized) drop's down from the menubar and waits to get information from user. Once the user clicks on a button, it returns the button value and then closes. My problem is that when I load a script that has the tell statement for the app, it launches the application (dropping down the dialog box when it shouldent). I cant seem to figure a way around this problem, I need the applescripts to be in the form they are, but I would also like to call custom dialog boxes that I create in PB. Does anyone have any ideas? Please let me know if this makes any sense..

Thanks

----

Hmm, I don't think I understand.  However, it sounds possibly useful to convert your master applescript to an applescript studio app and use the **call method** command to trigger these dialogs.

-General/KenFerry
