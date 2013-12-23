---
layout: page
title: DelegateMethodNotGettingCalled
---



My NSApplication delegate method is not getting called here.  Can someone tell me what I am doing wrong here? Thanks.

**Could you do the community a favor and check out the SandBox and HowToUseThisSite pages before posting further, please?**

    
//AppController.h
   .
   .
   NSApplication *app;
   .
   .

//AppController.m

- (void)awakeFromNib
{
    [app setDelegate:self];
}

//delegate method
- (BOOL)applicationShouldOpenUntitledFile:(NSApplication *)app
{
    return YES;
}

----

What's that code supposed to do?     app is nothing. You declare a pointer to an NSApplication called app, never assign anything to it, then seem to expect     [app setDelegate:self] to work in awakeFromNib. It won't; it's just sending a message to nil (what     app is pointing to.) Your delegate method isn't getting called, because your object isn't getting set as the delegate.

Change awakeFromNib to be

    
- (void)awakeFromNib
{
    [NSApp setDelegate:self]; //NSApp is a shortcut for [NSApplication sharedApplication]
}


----

I am new to objective-C, so forgive me if I am asking some stupid questions.  What I am trying to do is that I have a UI, and I want to use some of the delegate method in NSApplication to open my UI when users click the icon in the dock.  And I am really sure how to setup the delegate to my UI from NSApplication.  Please help.

----

In that case, read up on NSMenu, NSMenuItem, and the NSApplication objects. Also, check out this article [ http://www.cocoadevcentral.com/articles/000036.php ] about creating "Dock Menus".

*I don't think he wants a dock menu, he just wants to reopen a window when his dock icon is clicked. There was another post asking how to 'keep an app from quitting' or something a couple days ago. Here it is: CloseButtonOnUI *

Um, no, I don't think this is quite what he wants. He wants the UI to be visible when the app icon is clicked, not quit when the UI is invisible. Original poster, you might want to just set your window to be invisible when the application is inactive. Simple, isn't it? Do this in Interface builder.

*Read down that page where he says "What I really want to do is after users close my application, they should be able to open my app again by clicking my icon showing in the dock." He sounds like a Windows programmer that doesn't know the difference between a window and an application.*

Prognosis: Original poster, please re-read the introduction to Cocoa programming documentation found at developer.apple.com ...

