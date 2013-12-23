---
layout: page
title: InputCommands
---

I cant figure out an way to make commands like typing somthing and then hitting enter so somthing happens, can somebody help me?

----

Er, In a GUI?

Add a text field and button to a Cocoa window. Set the button's key equivalent to 'enter' (is there a diffferent/better way to set a button as the default? Or is this it?) Make a controller with an outlet for the text field and an action for the button. Hook up the outlet and the action. The action can grab the text from the field and interpret it.

Or do you mean simulating a console window?

-- AdamVandenberg

----

The easier way is to set the text field to send action 'only on enter' in InterfaceBuilder and then set the text field's target to the action method.  No button required (though you can still use it and hook it up to the same action if you want one).  -- Bo

----

I also need help Interperting it
And it would be nice to be able to simulate the Terminal window

----

BookBuildingCocoaApplications has a nice example of doing something similar.  They interact with a mathematical expression processing
engine.

----

It would be nice to not have to go out and buy a book or wait a while by ordering it online.

----

Then wait no more -- all the documentation you need is on your Mac OS X box.

Reading between the lines it sounds like you want to write a Terminal or Worksheet application. Terminal programs may be difficult for your average Cocoa programmer as you also need to understand and simulate the legacy that is the UNIX terminal. So I'm going to assume (since there are no details to contradict me) you want a Worksheet-like application. These are much easier for your average Cocoa programmer.

First, you're going to need a window w/ a text view. You'll want to subclass the text view so that you can intercept enter or return keys. Read up on NSResponder. NSText, NSTextStorage, NSTextView, NSWindow, all of which are on your hard drive.

Second, you're going to need to collect the command, pass it to a shell interpreter, and spit the results back into the window. Use NSTextView to get the proper range of text as an NSString, convert the string into a series of commands as an NSArray, feed that to an NSTask, run it, get the results back as a string and put them in the window. Read up on NSFileHandle, NSPipe, NSString, and NSTask.

If you need to parse the output into some kind of ordered information, perhaps for colorizing, read up on NSCharacterSet, NSColor, NSLayoutManager, and NSScanner.

Hope this helps. Feel free to ask detailed questions (i.e., "how do I do specifically XYZ"), and maybe you'll get detailed answers.

-- MikeTrent

----

I need all the resources I can get. I also Need a good place with Lots And Lots Ands LOTS and LOTS samples and a BIG forum For C Focused on C not on making Interfaces And Lots And Lots Ands LOTS and LOTS samples and a BIG forum for OpenGl too. If anybody can help.
How did Apple do there Spell checker?
Also how do you constanly update static text? like every second? Im making a geekclock for true geeks.
I do NOT want to make a worksheet, Lets just assume for now (I cant give details) I am making a carbon copy of the UNIX terminal.

----

I REALLY NEED a good website that is a good place with Lots And Lots Ands LOTS and LOTS samples and a BIG forum For C Focused on C not on making Interfaces And Lots And Lots Ands LOTS and LOTS samples and a BIG forum for OpenGl

----

Understand that this is a web-site for Cocoa programming focused on Cocoa programming, and we really aren't designed to teach people how to write Apple's spell checker or Apple's Terminal. Go check out GNUStep. They've got exactly what you're looking for: http://www.gnustep.org/ . They also have a wiki: http://wiki.gnustep.org/

-- MikeTrent

----

fine, iwas just wondering How about Making a clock?
----
Right. Everyone should make a clock. Here is mine: http://www.furrysoft.de/index.php?lang=en  :-)

----

Back to commands, What how do you simulate the UNIX terminal with one command like 

    

launchapplication /Applications/X11/



And the output:

    

Launching...



then it displays it. 
----
There were some good hints a bit up. Use NSTask and pass the path on to the /usr/bin/open command. Then print out Launching.... and after that do a NSWaitUntilTaskDone or similar.

----

Why use /usr/bin/open? /usr/bin/open is just a thin wrapper around AppKit's NSWorkspace, just call that directly (rather than wrapping a call to the wrapper, which is a bit redundant). --FinlayDobbie

