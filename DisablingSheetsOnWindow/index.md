---
layout: page
title: DisablingSheetsOnWindow
---

I want to be able to disable all sheets on a NSWindow, i.e. any attempt to start/attach a sheet is ignored.

Currently I attach the following code to the delegate of my window, and while it seems to work
well for the cases I've tried - I'm not convinced that this is the best or safest way. So any better suggestions?

    
// to be made delegate of NSWindow
- (NSRect)window:(NSWindow *)window willPositionSheet:(NSWindow *)sheet usingRect:(NSRect)rect {
    [sheet close];
    @throw [NSException exceptionWithName:@"NoSheets" reason:@"disabled sheets" userInfo:nil];
}


Meanwhile, I do realise that another option is to simply go through and change all the the places in my code that 
actually open sheets so that they check which window they will open on, etc... though that's more work compared to 
the above solution.

----

It may be easier but it's really evil. You have no guarantee that Cocoa will be in a reasonable state after that exception is thrown. You could even be leaking memory without knowing it now, and it may simply stop working at all in the future as Apple changes its code.

If you really must avoid the hard way, subclass NSApplication and override     beginSheet:modalForWindow:modalDelegate:didEndSelector:contextInfo: to check for whatever you need before calling super. But it would be much better to just find your code which shows sheets and fix it up to not do that when circumstances require.

----
Or...rethink your goals. If you are fine with the *sheets* but don't like them on the *window*, then use NSPanel, or subclass NSWindow and override     canBecomeMainWindow...and use that to figure out whether or not to open a sheet yourself. This should care of automatic sheets, too, like the save panel for a document. (NOTE: untested.)

On the other hand, if you actually want *no sheets* when the window is being displayed, consider making it modal. Yes, I know modal windows are somewhat evil, but it does limit the possibility of a random incoming sheet. Either way, it may still be best to go back and edit your code. (Trust me, changing either your window or     beginSheet:modalForWindow:modalDelegate:didEndSelector:contextInfo: *in your own application* is basically like saying "If 2 + 2 = 5, then the answer is 4." Which I saw as an anecdote in a computer book but which isn't really a good way to code.) The sooner you fix the problem, the easier it will be: imagine trying to fix it when your codebase has doubled in size.

Out of curiosity, why? (NOTE: if you say why, you will get others posting on the validity of that decision. Which you may or may not want to hear, but which is probably good in the long run.) --JediKnil

----
When the app (or rather one of the subviews of a window) enters fullscreen mode  then any save/open/pref/etc sheets are undesired.  On reflection I agree that going back and changing the original code is a far better solution. Depending on user testing I'll either make each sheet start conditional on fullscreen, or just toggle fullsceen. Thanks for responding to my late night coffee overdosed query .

