---
layout: page
title: PanelVsWindowForDialogs
---

I am a little uncertain to when I should use panels and modal dialogs.

For example I have an application where the user can record a script. After this he can execute it (and it works on the active document), he may also select to save it -- since it is not saved as a file, a custom dialog appears, asking for a name and optionally lets him select a hotkey for this script.

The user may perceive this as relating to the current document, thus a sheet might not be inappropriate -- but it really does not only relate to the current document, i.e. if he actives another document then he won't be able to record a new script there and save that, while the previous dialog is up.

So we may make an application modal dialog, but personally I really think these suck, and should generally be avoided -- and technically it is really not a problem to just let the 'save script' bring the dialog to front (so if selected again, the same panel is brought to front) -- and selecting "Save" in the window will work on the most recently recorded script.

But is this violating the styleguide?

And should this dialog be a panel or a window?

----

My rule of thumb is that if it should show up in the Window menu's list, it should be an NSWindow, if it should not, it should be an NSPanel. I'd say that this dialog should be a panel.

There are several ways to deal with the UI issue. For app-level scripts, you could have the typical "Record Script" and "Stop Recording" menu items. The "Stop Recording" item handles the save dialog. "Record Script" would be disabled until the "Stop Recording" action is complete. "Stop Recording" would pop up a dialog asking for a save name. I think it would be okay to make this dialog app-modal. "Stop Recording" would remain highlighted until the user makes a decision.

If the scripts are doc-level scripts, I think they should be saved using a document-modal sheet. I assume you have a "Record Script" menu item, which you would just disable the "Record Script" menu item until the sheet is closed.

Alternatively, you could treat scripts as a secondary document type. After recording, you would display a window with the recorded script and allow the user to inspect or alter it. Then he can save it via a sheet, like any document.

--DustinVoss

