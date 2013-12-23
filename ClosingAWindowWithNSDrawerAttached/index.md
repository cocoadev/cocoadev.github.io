---
layout: page
title: ClosingAWindowWithNSDrawerAttached
---



What is a good way to close an NSWindow which has an NSDrawer attached, so that the drawer is in the same state when the window reopens?

Context: I am a Cocoa newbie; I am trying to make a few adjustments to the nntp client OSXnews [http://osxnews.sourceforge.net/], partly privately for self-teaching purposes and partly to contribute to the project itself.  Its interface is based on Mail's, with a drawer listing news servers and the respective subscribed groups in an NSOutlineView, and the main window split, displaying the list of messages in the selected group in the top half and displaying the selected message in the bottom half.  Now, this window can be closed, and the current implementation in the delegate is roughly:
    
-(BOOL) windowShouldClose:(id)sender {
    if ([drawer state] == NSDrawerOpenState) {
        drawerWasOpen = YES;
        [drawer close];
    } else {
        drawerWasOpen = NO;
    }
    [mainWindow orderOut:nil];
    return NO;
}


Then when the window is to display again there is something like:
    
[mainWindow setKeyAndOrderFront];
if (drawerWasOpen) [drawer open];


This seems an awful lot of work (and also does not remember which side the drawer was on, though I can fix that).  Furthermore, there is a delay while the drawer closes, and on reopen a delay while it opens - if the order in the last code snippet is reversed the drawer appears before the window, then extends.

The window is set *not* to be released on close, so I thought deleting all of this and allowing Cocoa to look after itself would work.  It appears to be fine, but then when I close the window and click on a Finder window below where the drawer was, a white rectangle appears behind the Finder window, a click on this restores the window but not the drawer, and it requires a <code>[mainWindow makeKeyAndOrderFront:nil]</code> to fix things.

Any ideas?  Also please tell me if this seems way too long for a "Discussions" post!

Thanks  --  MichaelBannister

----

Drawers suck - you'd be better off mimicking the new Mail interface (though you could allow side-swapping). That said, I'm pretty sure there's an NSDrawer category out there that adds the methods you're looking for.

----

Drawers do not suck. They are sometimes overused. The new mail interface has some design flaws IMHO. Your rough implementation is the way to go. Just keep refining it until you have the behavior you want. An alternative is to catch the window close, and move it offscreen instead. Then you can move it back to original position on open, and it should look exaclty the same. The (rather serious) downside is that with this method you will have a window loaded into memory (and the window manager) that will not be displayed. I am no expert on the windowing sys, maybe there is a way around this. -JJJ

----

What design flaws? That it ships with a flexible space (which is easily removed) in the toolbar that moves all the toolbar items when you resize the mailbox split-view?

I almost like it better than the old drawer version. The only 'flaw' I see is that it can't be put on the right-hand side, where pretty much everyone had the drawer. I think it would be nicer if there was a normal (or custom?) divider and the 'add' and 'action' buttons were on the top but this is where you can easily one-up Apple.

But hey, don't listen to me - I took an app that had a split-view like that and completely replaced it with a drawer, opposite of what I suggested above. Apparently I did a good job because I got several donations for my "correction". Looking back though, I should have kept the split-view and added the option of using a drawer instead of doing an all-out replacement because even though I got a few donations there were several extremely vocal users (of the "drawers suck" variety). YMMV, blahblahblah - end ramble.

----

OK, so is there anyone who knows lots about Quartz (or whatever is used as the window manager) who can advise me further?  I should clarify that since I am not the designer/creator of this particular application, I do not feel it is my place to tear up his work; I merely wish to improve things in place, for the moment.
Thanks.  --  MichaelBannister

----
You can get the private NSWindow subclass that the drawer uses with a category like this (from memory, compiled here)..

    
@implementation NSDrawer (IWantThePrivateDrawerWindow)
- (id)drawerWindow { return _drawerWindow; }
@end


Not sure what all you can do with that but it might get you started.

