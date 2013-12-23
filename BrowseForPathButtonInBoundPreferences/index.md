---
layout: page
title: BrowseForPathButtonInBoundPreferences
---



I have panel represent my defaults via binding!
All works fine but for the browse button I have:

I have a textfield holding a path and want to offer a browse button. The button is not realizable without 'glue code', is it?
What's the best way to do it?

----
Don't know if this is the best way, but here's what I did. Just put the following code in a custom class, and add an instance of the class to IB. Then bind your text field's content value to a key (I used "template") in user defaults. -JediKnil
    
/** Make this your Browse button's action */
- (IBAction)chooseTemplate:(id)sender
{
    NSOpenPanel *openPanel = [NSOpenPanel openPanel];
	
    [openPanel setCanChooseDirectories:NO];
    [openPanel setCanChooseFiles:YES];
    [openPanel setResolvesAliases:YES];
    [openPanel setAllowsMultipleSelection:NO];
    [openPanel beginSheetForDirectory:nil
                                 file:nil
                                types:nil
                       modalForWindow:window
                        modalDelegate:self
                       didEndSelector:@selector(openPanelDidEnd:returnCode:contextInfo:)
                          contextInfo:@"template"];
    // @"template" is the name of the key you want to store in user defaults (where the object is the filename itself)
}

/** Called by NSOpenPanel */
- (void)openPanelDidEnd:(NSOpenPanel *)sheet returnCode:(int)returnCode contextInfo:(void *)contextInfo
{
    if (returnCode == NSOKButton)
        [defaults setObject:[sheet filename] forKey:contextInfo];
        // contextInfo contains the key -- this way you can have all your open panels store values in user defaults
}


----
Cool! Thought I could avoid glue code altogether now but that way is quite elegant :)

