---
layout: page
title: WindowCollectionBehavior
---

What do people know about "Spaces" and how Leopard determines where Windows are visible?  From what I've been able to determine (not much, I hope) there is a new method:

     - (void) setCollectionBehavior: (General/NSWindowCollectionBehavior)collectionBehavior; 

Of which there are 3 values for General/NSWindowCollectionBehavior:

     enum {
   General/NSWindowCollectionBehaviorDefault = 0,
   General/NSWindowCollectionBehaviorCanJoinAllSpaces = 1 << 0,
   General/NSWindowCollectionBehaviorMoveToActiveSpace = 1 << 1
};
typedef General/NSUInteger General/NSWindowCollectionBehavior; 

This is about all I've been able to figure out.  I've got an app for which a hotkey brings up a preferences window that needs to appear in any given space.  Right now, I open the window by doing:
        [prefWindow setCollectionBehavior: General/NSWindowCollectionBehaviorCanJoinAllSpaces];
    [prefWindow makeKeyAndOrderFront: nil];  

This makes it appear in every space.  Which is fine, I suppose, since it will apply behavior that might or might not extend beyond the current space... but, the window really doesn't need to appear in every space.

This behavior just doesn't seem very configurable to me.  So I can either have my window join ALL spaces or just join just the ACTIVE space (the default value appears to join the active space).  I just feel as if I have no control over where my windows appear.  If the implications of the settings within my app extend to applications outside my space, I want my app to appear there as well.  If we pretend, for a moment, that my app extends/modifies/complements application "X", I want to make sure when application "X" is visible that my app is too.  Normally, with windows of level "General/NSFloatingWindowLevel" (et. all, this is just an example) it was was pretty easy to be sure my window would be around to see.  But now, I find that activating my app while in the "wrong" Space will render me useless unless I show myself in ALL spaces (which seems excessive).

---- 

So, I suppose I'll pose a few questions before I go:

1) Can I determine (easily?) which apps are assigned to which "space"?

2) Can I assign my windows to specific spaces without breaking an arm/leg?

3) If I have a General/WindowID, can I figure out in which space this window exists?

I'm sure there's a million more questions one could derive from here.  I'm hoping I either completely missed some documentation (I want my day refunded then) or there's something else stupid I'm missing. I feel like with a new feature like this, my three questions should be trivial.
