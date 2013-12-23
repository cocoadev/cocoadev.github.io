---
layout: page
title: CreatingASplitViewEditorLikeXCode
---



*[ Moved from InterfaceQuestion ]*

----

Hi, I'm new Mac/Cocoa, and I'm looking for help with a control, used in Xcode.
In the Xcode window, you can split the view(s) into three. The left column (Groups and Files),
and then the right side, where you have the File name, and below it an editor window.
Between the File name and editor window, is a bar that contains combo boxes (listing methods).
I would like to know how that bar and combo boxes is created and populated.
I can't seem to find the proper control in interface builder. The closest match is NSComboBoxCell,
but it seems to need to be contained within an NSTableView ?

If you can point to any documentation or examples, would be greatly appreciated.
One more question, does Interface Builder contain all the controls available on the Mac, or are there
additional palettes that can be added ?

thanks
Steven

----

There is no ready-made control or solution for this. You'll have to do this in code. Basically, the steps are (from "single-editor" mode) 1 - retain (to keep from being released) and remove the editor/scroller from the parent view. 2 - Create a split view and add it to the parent view. 3 - Add the original editor/scroller to one of the split view panes and release it. 4 - Create a new editor/scroller and add it to the other split view pane. Just reverse the process to "collapse" the editor back to normal. You're swapping controls in and out depending on the 'mode' you want your GUI to reflect.

As to the button itself, all (well-behaved) controls use cells. NSButton has an NSButtonCell. See the NSControl Apple documentation for more information on controls and cells. Though you can use cells in table views (which is what the IB palette cells are there for, mostly), cells are used in quite a bit of places and are quite flexible. In XCode the button you're referencing appears to be a simple button. Forget about its cell. It just needs to be small enough and configured correctly to look right. Regarding placement, see the TextEdit example on your hard drive in /Developer/Examples, paying particular attention to their custom scroll view code. They've placed a zoom control inline with one of the scrollers. You can subclass a scroll view and override its     -tile method to make the vertical scroller a bit shorter and position your button to fill the remaining space to look like XCode.

You will need to familiarize yourself *very well* with how to create / manipulate GUI controls in code. Then focus on split views, then custom views and drawing. There're plenty of pages here and quite googlable external resources on each of those subjects. You should also focus on subclassing and overriding methods to change / augment the behavior of existing objects / controls.

----

**Meta-Discussion**

This is another good Q-and-A, but the title of the page really does not do justice to the sophistication of the inquiry. It should be refactored or retitled to take it out of MailingListMode. Much more could be discussed here.

----

I've taken your advice and altered the description, hopefully to change the course of discussion to be more appropriate for this format. Thanks, Steven.

----

That's fine, Steven, but your response misses my point. With a title like I**'nterfaceQuestion, this page could be about *anything** having to do with interfaces. That is what places it squarely in MailingListMode. Furthermore, because of the scope and variety contained within the question, we don't know even now if your question is about split views, or inserting controls in the separators between split views, or even if all your questions have been answered. One might move the whole discussion to CreatingASplitViewEditorLikeXCode. I mean no offense, but questions such as yours, incorporating such specificity in content deserve specificity in their titles, and at least recommend that their authors exercise some creativity. Furthermore, if characterizing the question in a specific way is beyond you, you may quite possibly be investigating a matter without the preparation necessary to understand the answers you might receive (though this is by no means a certainty). I salute your curiosity and wish you much success in learning to use the Cocoa frameworks.

----

I've taken the liberty of moving this content to a more appropriately-titled page.

