---
layout: page
title: NSSavePanelnsideCustomView
---

Ok, I wish to put an NSSavePanel object inside a custom view object (Namely an NSView object). This is so that the NSSavePanel is surrounded by custom stuff (2 buttons, an NSTextView and an image). What I am trying to achieve is a "Wizard" type of panel, and at the end the user chooses where they wish to save thier work.

I know that I can use accessoryView in the NSSavePanel but will that do what I want? I don't understand how it works, as far as I am aware setting the accessoryView will add extra features to the NSSavePanel, not surround it with what I want. Thanks for any help.

----

First of all, a warning: most Mac users don't like "wizards," and barely tolerate "setup assistants". It's a lot better to do something like Xcode's "New Project" menu option, specifically the second panel. This way you can have whatever standard interface you need, in keeping with the rest of your panels, and show where the file will be saved (the Project Path text box), but also allow people to use the standard interface for choosing where to save something (the Choose button). That being said, you could try setting up the panel with the normal NSSavePanel methods, then pull the content out using     [NSSavePanel contentView]. This may have some unwanted results, however; content views can (but don't always) act funny when detached from their window of origin. --JediKnil

----

Ok then, well that makes it a little easier, but what do you mean by setting up the panel with the normal NSSavePanel methods? And also what do you mean by pull the content out using     [NSSavePanel contentView]? Rather, how do I bring down a panel like that which is done when clicking "choose" in Xcode, is that panel a type of NSSavePanel?

----

That kind of panel is called a "sheet", but yes, it is a type of NSSavePanel. Specifically, code like this will show one attached to a window called     mainWindow. --JediKnil
    
NSSavePanel *savePanel = [NSSavePanel savePanel];
[savePanel setCanCreateDirectories:YES]; // or any other configuration stuff
[savePanel beginSheetForDirectory:@"~"
                             file:nil
                   modalForWindow:mainWindow
                    modalDelegate:self
                   didEndSelector:@selector(savePanelDidEnd:returnCode:contextInfo:)
                      contextInfo:NULL];
// Show the Save sheet in the home directory
// (if this doesn't work, try file:@"")

// ...somewhere else...
- (void)savePanelDidEnd:(NSSavePanel *)savePanel returnCode:(int)code contextInfo:(void *)unused
{
    if (code == NSOKButton)
    {
        // save to [savePanel filename]
    }
}


----

No sorry, I meant how do I just make the NSSavePanel act like the "choose" panel as in xcode. Can I customize the NSSavePanel so that it says "choose" rather then save on the button? Thanks.

----
Ah, well in that case, the first button (that calls up the panel) is just a button, and you can name it whatever you want. The one you're probably talking about can be set using NSSavePanel's     setPrompt: method. --JediKnil

----
Yep, and instead of using NSSavePanel I figured out to use NSOpenPanel, and set     [openPanel setCanChooseDirectories:YES]

