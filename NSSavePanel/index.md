---
layout: page
title: NSSavePanel
---

NSSavePanel is the default Save Panel provided by AppKit.

It includes File Browsing capability, File Naming, OK and Cancel buttons.

----

If you are writing a NSDocument based app, you might want to customize the save panel with some extra information (in NSTextFields) or choices (NSButtons, etc).

NSSavePanel has <code>- setAccessoryView:(NSView *)view</code>, which allows you to add a custom view you can instantiate in IB, or on the fly. 

You can't simply tell NSSavePanel to always use a particular accessory view in your <code>awakeFromNib</code>. You need to know which NSSavePanel instance is being used at a particular time. The docs for NSSavePanel tell you that they're cached, and you can call <code>+ savePanel</code> to get a pre-existing one, but note: **That savePanel will be wiped clean** - no accessoryView.

You need to know which instance is being used at the time <code>saveDocument</code> is being called. There is a handy method of <code>NSDocument</code> called <code>prepareSavePanel:</code> that lets you do this:
    
- (BOOL)prepareSavePanel:(NSSavePanel *)savePanel

From Apple's Docs:
""Invoked by <code>runModalSavePanelForSaveOperation:delegate:didSaveSelector:contextInfo:</code> to do any customization of the save panel. Returns YES if successfully prepared, and NO otherwise. The default implementation is empty and returns YES.""

**Example:**

Providing the user with an NSPopUpButton of choices that are shortcuts to directories of interest. Suppose you have an array of directories <code>_dirs</code>, an NSPopUpButton <code>_dirsButton</code> with directory names as titles of the items, and since we want to manipulate the NSSavePanel from <code>_dirsButton</code>'s action method, we need to keep the savePanel around in an instance variable, <code>_activeSavePanel</code>.

Here's what <code>prepareSavePanel:</code> looks like:
    
- (BOOL)prepareSavePanel:(NSSavePanel *)savePanel{
    _activeSavePanel = savePanel;
    [savePanel setAccessoryView:savePanelAccessoryView];
    return YES;
}

Because it's so simple, we always return YES.

And here's the action method for <code>_dirsButton</code>:
    
- (IBAction)setSaveDirFromPopUp:(id)sender{    
    [_activeSavePanel setDirectory:[sender title]];
}

<code>setDirectory:</code> sets the currently viewed directory to the path you pass as an NSString to it. 

Of course, in a real application you'd want to do something nicer than have entire pathnames as item titles, but that one's left as an exercise to the reader.

--MichaelMcCracken

----
I created a custom view in IB and set it to be the accessory view for my NSSavePanel using <code>setAccessoryView</code> in <code>prepareSavePanel:</code>. However, when the user resizes the NSSavePanel, it appears that my custom view doesn't resize with the NSSavePanel. As a result, my custom view gets stranded in the center as the NSSavePanel gets wider. I'm not actually sure whether 1) the custom view doesn't expand with the NSSavePanel, or 2) the contents of the custom view are not expanding with the custom view. I've tried setting the Autoresizing springs in IB for both the view and the contents within. Any idea on how to solve my resizing issues? Thanks!

