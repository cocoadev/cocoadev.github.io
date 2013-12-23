---
layout: page
title: ApplicationOpenFileandAwakeFromNib
---

Ok, this must be simple but I can't figure it out:

I am adding a shareware license thing to my program. But I can't get everything to work at the same time There are two parts. 

1. Check for valid licence, and time expiration of demo, and pops up an NSRunAlertPanel. This is contained withing awakeFromNib - and works.

2. THe user can double click a lisence file, to unlock the application. This places the license file in the correct placen and also pops up an NSRunAlertPanel, to thank the use for registering. This code is contained within application:openFile. This also works, but only when I disable the awakeFromNib code.

When I drag my licence file onto the application the awakeFromNib is running first and poping up its NSRunAlertPanel. This appears to not only prevent the openFile NSRunAlertPanel window from opening, but prevents any of the openFile function from running.

What I want to happen is for the file to be opened before awakeFromNib is called. Any ideas?

----
Run your alert on a timer?

----
Yep, that's not a bad idea, but I think that there are a couple of problems. Since the openFile function has an NSRunAlertPanel, we need to wait for the user to press OK. We don't know how long to wait for. Second, I don't want to make the loading of my program unnecissarily slow. There must be a better way?

----
Actually I gave that a try, anyway. And it doesn't work. I put in a delay of 1s and even disabled the fileOpen NSRunAlertPanel. Still doesn't working. I am guessing that awakeFromNib has to complete before fileOpen is called.

----

Do yourself a favor and post your (relevant) code. It's easier to comment on this kind of problem (by commenting / amending your code) than it is to conceptualize from a vague description.

----

So wait, you want your openFile: code to execute before awakeFromNib: is called on the app delegate in your main nib?  I believe that might be a pretty tall order, considering the main nib (the one specified in Info.plist) is unarchived when the app is launched.  (see http://developer.apple.com/documentation/MacOSX/Conceptual/BPRuntimeConfig/Articles/PListKeys.html#//apple_ref/doc/uid/20001431-107221 ).  So NSApplication will unarchive whatever nib is specified in the NSMainNibFile key of your Info.plist as part of its launch phase, which means NSNib is going to call awakeFromNib: for the application's delegate (or whatever object you're using to receive awakeFromNib:).

----
Thank you. That is very clear. It seems I need another way of doing this, but I am not sure what. Proabably a welcome screen, to perform the file load operation/ or nothing if just launched. Anyone got any tips on inserting a welcome screen, before the main window is loaded?
----
Just put you "main" window in a separate nib hand have no window at all in MainManu.nib.  Create some subclass on NSObject to handle loading files including additional nib files  and display licenses etc.  Instantiate the custom class in the MainMenu.nib and make that instance the application's delegate.  Then implement applicationDidFinishLaunching: in the custom class to implement whatever you want.

This is very basic use of Cocoa.  Looking at the TextEdit sample on your hard disk will give you lots of inspiration.  Books like BookCocoaProgramming and Building-Cocoa-Applications-Step-Guide explain will guide you. 
----
See http://www.stepwise.com/Articles/2006/eb1/index.html for another source of inspiration.

----
I think the poster two replies above might have meant use applicationWillFinishLaunching:, because applicationDidFinishLoading: is called after openFile:.

