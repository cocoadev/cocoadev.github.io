---
layout: page
title: NSOpenPanelScreenPosition
---

Is there a way to always have an open panel keep its last screen position.
Every time I call:

    NSOpenPanel *openPanel = [NSOpenPanel openPanel];
    result = [openPanel runModalForDirectory:movieDirectory file:nil
             types:fileTypes];
        
    if (result == NSOKButton){...

The open panel centers itself on the screen. I would prefer it to stay in
the last position that I moved it to.

I am assuming I would have to do something like:
[openPanel setDelegate:controller];

Then
- (void)windowDidMove:(NSNotification *)notification

Would I then get the NSRect of the notification object

I can't seem to find the answer...Thanks in advance.

Chad Williams
http://www.flexvisual.com

