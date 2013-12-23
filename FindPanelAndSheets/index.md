---
layout: page
title: FindPanelAndSheets
---



I put a NSTextView in a modal dialog window. Everything is working fine with the "for free" Find panel.

Then I use this window as custom sheet and launch a document-modal sheet with NSApp::beginSheet.

Too bad, in the "for free" Find panel, the buttons (and only the buttons) are disabled.

Note that every key shortcuts are working well for searching, and the spell checking pane work perfectly, only the buttons of the default Find panel are disabled.

How should I solve this strange behavior?

----

No immediate solution, but the reason for this is that palettes are supposed to be disabled when modal dialogs are up. The idea of a modal dialog in which the find panel is needed sounds a bit scary.

----

In my case, the modality of the text editor is appropriate. The workflow of the users' actions is constrained, as principal motivation for the usage of sheets.

BTW, I do not see why the other palettes like the spell checker are working well. Moreover, the look and feel of the Find panel is different (classic window instead of utility window): I do not imagine any valid explanation than historical issues.

Hence, some help to solve this case is welcome :-)

----

How to enable the find panel in modal dialogs?

*The whole point of a modal window is to shut out other windows and force the user to pay attention to the dialog. If you need a find panel (i.e., long error report), you should probably make your dialog a normal window, which would allow the user to do other stuff in the mean time. What exactly are the circumstances in which you need a find panel? --JediKnil*

The main window contains a WebView that present the current state of the managed object context. Above it, a toolbar allows to manipulate the underlying data. For instance, an editor window is presented as sheet mainly because I don't  update the content of the WebView in real-time. I only update the WebView when the user close the sheet.
The HIG agree to use document modal sheets in this case. During edition  of data, the user is not able to do other things, the workflow path is easier to follow this way.

*Well, I do agree with the general idea; however it's not necessarily perfect. For one thing, many features besides Find (like Save and Print) are probably disabled in this mode. A better solution might be to follow the Mozilla strategy: open a separate window for editing and allow the user to switch between them. This could also be useful when they want to see the source code before and after...they could open two editing windows with "snapshots" of the current code. Then when you save, it automatically updates the main window but keeps the editing window open. Just a suggestion. As for making the Find panel work, you may want to look at the NSTextView method     performFindPanelAction:, which should open the find panel when called. You probably *won't* be able to enable the menu item, but you could have an invisible (or even visible) button on your panel with a key equivalent of "Command-F" to open the Find Panel. --JediKnil*

----

No way, I definitely need to enable the find panel's buttons in modal dialogs!

----

I thinked about it twice, and now, I'm convinced that your are right. A separate window for the editor is the way to go.

----

I found that a Window class must return YES on canBecomeMainWindow: for the buttons to work. Is there a way to make the sheet use a special subclass? 

----

The documentation says: "NSWindow�s implementation returns YES if the receiver is visible, is not an NSPanel, and has a title bar or a resize mechanism. Otherwise it returns NO."

Since sheets do not have title bar, they can not become main window per default.

Hence, I inherited from NSWindow and implemented the -(BOOL)canBecomeMainWindow; method to return YES. Everything is working now. Thank a lot for your insight.

