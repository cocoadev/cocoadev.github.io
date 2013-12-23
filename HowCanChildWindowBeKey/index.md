---
layout: page
title: HowCanChildWindowBeKey
---

 If I make a window a child of another window, the parent should remain key/main but the controls in the child window should be drawn active. Sheets and Drawers do this without stealing focus from the parent window and deactivating it, but when I make my subwindow key, the parent dims (or alternatively, if I don't make it key but merely order it to the front, the parent remains active but the controls in my subwindow are drawn inactive). How do I make the subwindow active without deactivating the parent? In other words, what's the trick that sheets and drawers pull?

(Note: I did attempt to ask the same question a few days ago but nobody stepped up to the plate - I'm hoping that by rephrasing the question someone might recognise the problem and help me out! ;-)

----

Before you post on this wiki, you should really read HowToAskQuestions and MailingListMode.  You might have better luck getting a response if you ask on irc.freenode.net#macdev, or on the cocoa-dev mailing list.

That said, sheets and drawers are entirely different UI components.  Look at     -canBecomeKey and     -canBecomeMain.
----
Go read this:
http://developer.apple.com/documentation/Cocoa/Conceptual/WinPanel/Concepts/ChangingMainKeyWindow.html#//apple_ref/doc/uid/20000236-128794
and this:
http://developer.apple.com/documentation/Cocoa/Conceptual/WinPanel/Concepts/UsingPanels.html#//apple_ref/doc/uid/20000224

Essentially, use a NSPanel configured as a utility window for your subwindow. Your parent window can then keep its main window status while the panel becomes the key window. Both windows will be active and will be drawn with controls enabled. -CS

----

Thanks, though I have read those docs many times. Unfortunately they don't give enough information. When you say "configured as a utility window", what exactly do you mean? There is no specific API to make a panel a utility window, but there is     - setWorksWhenModal: which I've tried, with no useful effect. Perhaps this is caused by another aspect of my code, which is that having displayed the window, I enter a tracking loop of my own which pulls events off the window's event queue and handles them. I do this because the actual situation is in fact a menu-like window that pops up, tracks, then disappears. By handling the loop myself I can have a single entry point into my code that does the whole thing, returning control exactly back to the caller at the same point (i.e. no need for other callbacks or other notifications). Perhaps what I need to do instead is set up my own modal run loop? Again however, the docs are not completely clear on this - they explain the how to some extent but not really the why or when - so it's unclear if that what's needed. Any additional pointer on this topic would be greatly appreciated.

By the way - I don't want my subwindow to become key, so it may be that you've misunderstood. I want it to become active (i.e. controls are shown active), but focus in the main window remains set. This is identical to a floating panel that is neither key nor main yet has active controls such as buttons and sliders. Text fields will make the panel key in this case, achieved using     - setBecomeskeyOnlyIfNeeded:, which I'm doing. If I force my window to become key I get active controls, but of course that steals focus from the main window which is not what I want.

----

Mark the Utility Window checkbox in IB.

----

The window is created programatically (as it's a fully custom window with no border). What does the utility checkbox actually set? I am overriding     - canBecomeKey to return YES, though I just realised that the title I gave this page is incorrect - I don't want key, I actually only want active.

