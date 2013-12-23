---
layout: page
title: MenuValidationAndKeyEquivalent
---

I have Safari-style tabs in my MDI application which means, that when more than one document is shown in the same window, command-w should close only the current document, not the window, otherwise, command-w should do the normal performClose:.

So basically I must change the key equivalent for the two menu items (Close and Close Current Document) based on wether one or several documents are shown in the window.

I figured that this could be done from validateMenuItem: in my window controller (being the delegate for the window), and as such, it can. The problem is, what if another window is active, and responds to performClose:, then my delegate is not asked, and the key equivalent which was most recently set for this item, will be used.

Any suggestions to how I can gracefully solve this problem? I have thought about adding a category to NSMenu which always validate items with the performClose: action, and set the key equivalent, but I don't like this solution, because the window may already overload this method, and so I would need to do MethodSwizzling or similar.

*I just tried the Menu Validation-scheme, but it does not work -- I think NSMenu tries to do some dynamic key equivalent clash-avoidance, at least the qualifier for Close Current File ends up as shift-command-w, even though I set it to command-w only. Another problem is that performClose: is implemented by the window and thus not validated by the delegate (but that can easily be solved).*

----

I solved the same problem via making the AppController delegate for the File Menu and then implementing:
    
- (BOOL)menuHasKeyEquivalent:(NSMenu *)menu forEvent:(NSEvent *)event target:(id *)target action:(SEL *)action {
    [menu update];
    return NO;
}

which works beginning with 10.3 which introduced menu delegates. Note that after having returned NO the menu will perform the valid KeyEquivalents, so saying NO does not stop the menu.

DominikWagner
----

You could always subclass NSWindow, which I don't think would be inappropriate in this case.

*Not that easily, because a lot of windows are not instantiated by me (i.e. all "standard" panels), and so would not use my subclass.*

Allow me to show my ignorance of Document centric programming here, but, what's keeping you from using IB to set what method is called when the close menu item is clicked? I did that in my application, having the internal model unload and clear the gui state, but not close the window.

----

That would break the FirstResponder mechanism. I don't see why subclassing NSWindow and overriding performClose: to check for open tabs wouldn't work -  standard panels would have the standard performClose: method called, and MDI windows of the custom NSWindow subclass would have the overriden performClose: called.

----

The problem is that there are two menu items:

*Close Window -> performClose:
*Close Tab -> closeCurrentTab:

When a tab is present, "Close Tab" should have the key equivalent of command-W, and "Close Window" should be command-shift-W. But when there is no tab, "Close Tab" should of course be disabled, and command-W should be given to "Close Window".

I have now set it up so that I change the key equivalence modifiers in windowDidBecomeKey: and windowDidResignKey: (plus when the state of tabs change). The downside of this is of course, that my window controller needs to be given pointers to the menu items, which would not have been necessary, if I could have set the modifiers in validateMenuItem:, but this is not possible for two reasons, one being that when another window get focus, the last modifiers set will stick, and the other being that Cocoa is weird in that it doesn't respect the modifiers I set, most likely because of clashes (but clashes which should be resolved when all items have gone through my validate method).

*It seems the extra shift is because:*
    
[item setKeyEquivalent:@"W"];
[item setKeyEquivalentModifierMask:NSCommandKeyMask];

*Will set the shift modifier (sticky), because the key is given in uppercase.*

----

I solved a similar problem by overriding NSDocuments respondsToSelector, ending it with a call of     [[super class] instancesRespondsToSelector: ]
Not a pretty sight, but it worked.

