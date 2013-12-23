---
layout: page
title: DocumentFinishedLoading
---

Aside from the timer method (as described in AutoRunCode) , which seems cumbersome for doc based apps, is there any kind of equivalent to applicationDidFinishLaunching for an NSDocument. I have an NSBrowser which renders a preview when one of its items is selected. I want to be able to have the first item selected when the document is created, and then have the original preview generate.
The problem is, when the NSDocument does init, windowControllerDidLoadNib, and everything else I've tried, the NSBrowser still hasn't been loaded, so retrieving its selected cell returns nil. Obviously, applicationDidFinishLaunching won't work because, even if I could figure out how to connect the NSDocument to its delegate, it would still only work when the first document was launched. I would simply fake the behavior and have it load some kind of default information, but the NSBrowser is showing a list of plugins which can change.

----

Have you tried awakeFromNib? However, be careful if you use this because it may be called more than once per NSDocument instance (during the Save As process). So, don't put anything in here that shouldn't be called more than once. If that doesn't work, you could try an NSWindowController and override the windowDidLoad method. --RyanBates

----

Hmm... I hadn't tried that, but it still doesn't work. I think that the problem is that it has to load the column before it can select the first row. I can't figure out how to find out when it has loaded the column. I subclassed NSBrowser into a class called NotifyingBrowser and am trying to find the method that calls the delegate for operations like     browser: createRowsForColumn: inMatrix: . If I can find that, I should be able to override it, call super on it, and then tell my delegate     browserDidLoad:self .

----
OK. I got it working now. I overrode the addColumn method of my subclass and told it to call doClick on itself.

