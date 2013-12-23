---
layout: page
title: NSWindow
---

The NSWindow class takes care of keyboard navigation, the field editor, and the default NSUndoManager.

See Apple's documentation at http://developer.apple.com/documentation/Cocoa/Conceptual/WinPanel/index.html.

Other useful pages:

* GettingTheParentWindow
* BorderlessWindow
* NSWindowLevel
* SubclassingNSWindow


----

The Undo menu item sends the **undo:** ActionMethod to the FirstResponder. None of the 
built-in classes declare a public **undo:** method (though NSUndoManager does declare 
**undo** -- note the missing colon). So how is it that the Undo menu item works?

It turns out that NSWindow declares an **undo:** message privately, and thus is the default
 first responder for the Undo menu item. It also declares **validateMenuItem:** to set the
 Undo menu item title.

----

The     setExcludedFromWindowsMenu: method doesn't appear to work with NSPanel. They are always excluded from the Windows menu.

----

To make a window's controls always look active, override the     _hasActiveControls method to always return YES.


----

Saw this on the cocoa-dev mailing list:

On Mon, Oct 14, 2002 at 08:59:32PM -0500, Donald Brown wrote:
Our user testing has found that some users who have recently come from OS 9
have been hitting the toolbar hide/show button when trying to zoom the
window, and then suddenly have the toolbar disappear without understanding
why.  Is there any way to have the window NOT show the button in the toolbar?

Not exactly documented, but you can do it this way:

 NSWindow *window = ... your window ...;

 [window _borderView] toolbarButton] removeFromSuperview];

To add the toolbar hide/show button to a window, just add a toolbar to the window. It is included free, with the toolbar!

----
[[NSWindowLevel enums:

* NSNormalWindowLevel - The default level for NSWindow objects.
* NSFloatingWindowLevel - Useful for floating palettes.
* NSSubmenuWindowLevel - Reserved for submenus. Synonymous with NSTornOffMenuWindowLevel, which is preferred.
* NSTornOffMenuWindowLevel - The level for a torn-off menu. Synonymous with NSSubmenuWindowLevel.
* NSModalPanelWindowLevel - The level for a modal panel.
* NSMainMenuWindowLevel - Reserved for the application's main menu. (The menu bar in OSX)
* NSStatusWindowLevel - The level for a status window.
* NSPopUpMenuWindowLevel - The level for a pop-up menu.
* NSScreenSaverWindowLevel - The level for a screen saver.
* kCGDesktopWindowLevel - Under the desktop. (Still accepts mousedown so desktop is useless where the window is located)


Use these constants with [window setLevel:] to order your window above or below other onscreen objects.

*They're just ints, so, for example, if you want your window to appear above the desktop, but below desktop icons, you can use     [window setLevel:kCGDesktopWindowLevel + 1]*

----

In 10.2 there is an API for accessing the borderview buttons, and you can remove it that way. However, I found that it left a rect in the titlebar where you can't drag the window... Bizarre.

I made the cocoa-dev post method quoted above, and I didn't have problems removing it and still hvaing the window draggable in 10.1 - haven't tried it that way in 10.2.  --NicholasRiley

----

If you create a window with the style NSBorderlessWindowMask and want to be able to accept keyDown events you must also implement:
 - (BOOL)canBecomeKeyWindow
 {
 	return YES;
 }
In addition to the FirstResponderTrio

----

Some quick code to dynamically resize a window to a desired size while keeping the window position fixed:

*removes old crappy code*

*adds new and improved but still crappy code*

 - (void)resizeWindowOnSpotWithRect:(NSRect)aRect
 {
     NSRect r = NSMakeRect([self frame].origin.x - 
         (aRect.size.width - [self frame].size.width), [self frame].origin.y - 
         (aRect.size.height - [self frame].size.height), aRect.size.width, aRect.size.height);
     [self setFrame:r display:YES animate:YES];
 }

-- MatPeterson

----

Create an NSWindow category for this. It's exceptional useful for adding polish.
 - (void)centerWindow
 {
      NSRect visibleFrame = NSScreen mainScreen] visibleFrame];
      NSRect windowFrame = [self frame];
      [self setFrame:NSMakeRect((visibleFrame.size.width - windowFrame.size.width) * 0.5,
      (visibleFrame.size.height - windowFrame.size.height) * (9.0/10.0),
      windowFrame.size.width, windowFrame.size.height) display:YES];
 }

--[[MatPeterson

----

An alternative to the centerWindow category above of course would to just use the NSWindow's center method.
 [self center];
- or -
 [someWindow center];

----

Yeah you could, but my code centers it exactly on the screen rather than centering it and having the window almost hitting the menubar (at least on my systems).

----
I have a strange problem, unsure if its a Mac OS X bug, but when I display my window, a black rectangle the exact bounds of my window flashes on screen and then vanishes, having been replaced with the window. This is very strange and something I have not seen before. Anyone have any ideas? -- MatPeterson

----

Perhaps your window is being made visible in some way before you call your center method? Make sure 'visible at launch time' isn't checked in the window's nib.

I believe NSWindow's center method actually shows the window at what used to be called alert position - slightly above centered so the user is sure to notice it.

---- 

That's what the docs describe it as, which is a kinda strange position I think at least.

As for the window, its a custom window generated in the code and no amount of tweaking has removed this black frame appearing, although I am beginning to think more and more that its simply Mac OS X needing a restart.

----

Make sure you're setting the window's backing store and defer attributes to however they come by default in IB. See [http://developer.apple.com/documentation/Cocoa/Conceptual/WinPanel/Tasks/SettingWindowImageAttr.html]

----

It's all quite standard and I have used this code in other applications with no problems.

You're not perhaps creating the window in a thread other than the main thread, right? That's unsupported, and can often cause white or black boxes the size of the window to appear. -- UliKusterer

Well, without seeing the complete code, it's hard to say anything further. Also, I fixed a typo in the above     centerWindow code, replacing     visible.size.width with     visibleFrame.size.width

----
How do I hide a NSWindow? There exists something like [myWindow setVisible:NO]? Thanks!

Yes (I respond to myself)

 [window orderOut:nil]; // to hide it
 [window makeKeyAndOrderFront:nil]; // to show it

--RoTo

----

The API docs for NSWindow (http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSWindow.html ) include this tidbit:

 *'Note:*' Although NSWindow inherits the NSCoding protocol from NSResponder, 
 NSWindow does not support coding. Legacy support for archivers exists but
  its use is deprecated and may not work. Any attempt to archive or unarchive 
 an NSWindow using a keyed coding object raises an NSInvalidArgumentException.

To which I say: "huh? Do we not archive NSWindow every time we include a window in a nib?  Pray, what is my misunderstanding.."

..and then I search cocoabuilder and answer my own question.

In http://www.cocoabuilder.com/archive/message/2004/8/17/114828 , Vince DeMarco said

 In IB  we archive  a placeholder object then that object  gets 
 converted to a window.

----

How can I change the color of the text in the title bar? Is there an NSWindowTitleBar? I can't seem to find anything on Google about this. -- JasonTerhorst

NSTitledFrame(private) has _drawTitleStringIn:withColor:  You can override this method.

CocoaMovieTheater (http://www.zathras.de/) includes private headers of window frame classes. -- Afunishi

Note that I took down MovieTheatre ages ago, but since this page seems to be leading so many people to my site, I put up the class that I used to do custom window colors separately. Find it at http://www.zathras.de/angelweb/sourcecode.htm#UKCustomWindowFrame . Oh, and Afunishi, please don't directly link into the archives on my web site, link to the sourcecode page that contains a description for each of them. Thanks! -- UliKusterer

----

I'm having trouble changing the size of a window. I use this code to do it, in the initWithFrame: method of a subclassed NSView in that window:

 NSRect newFrame = self window] frame];
 		newFrame.size = NSMakeSize(TOTAL_WIDTH, TOTAL_WIDTH);
 		[[self window] setFrame:newFrame display:NO];

Nothing happens. TOTAL_WIDTH is a constant, #define'd to a certain value.

*In     initWithFrame:, the view is not yet in the window. Use     awakeFromNib. Or better yet, put this code into a controller where it belongs, instead of in a view.*

----

Newbie Question: 

I have been working on using [[CGCaptureAllDisplays and I want to set the level of my window above that of CGSheildingWindow...

Heres what I've done...

[window setLevel: CGShieldingWindowLevel() + 1];
		
However I am apprently ment to set window like this...

NSWindow *window = ... your window ...;

What do i put in the ... your window ... bit?

--TomLynch

----

This just means you need to obtain a reference to your window. If you have an IBOutlet set up that points to your window, you don't even need that line ... just use the name of your outlet. For example, if you have this in your header:

 IBOutlet NSWindow * myWindow;

Anywhere you see "window" being used above, just use "myWindow" instead, since it's already a reference to the window you want to use in that code.

----

Quick question:
I've been looking at the resizing of windows and it works fine, but the content doesn't behave like I have set in IB. When the the frame is enlarged the content moves down with it, instead of stretching like what was set. I'm sure it's really easy, or I'm just missing something.

Phil

----

You need to set the autoresizing mask of the view. Assuming that you're working in IB, this is on the cmd-3 inspector.

Note that views can be deeply nested, so it's easy to miss one or more views when setting up your autoresizing masks. The simplest solution is to switch the nib window into List View, then drill down through the outline making sure that *every* view's autoresizing mask is set correctly. *�boredzo*

----

How would I get the relative z-order of each of my application's windows in a given window level? I'd like to save the state of my application's windows when it closes and then restore that state (including the relative z-order) when the app is opened. -Daniel

----

Function **NSWindowListForContext** gives a list of all windows in an application. This list is sorted by the z-order of windows on screen.
The windows list contains Core Graphics window id. Method **windowWithWindowNumber:** of NSApplication retrieves NSWindow object from a window-id.

 {
     int numberOfWindows=0, iw;
     int *listOfWindows;
 
     NSCountWindowsForContext([NSApp contextID],&numberOfWindows);
 
     if (numberOfWindows>0)
     {
         listOfWindows=malloc(numberOfWindow*sizeof(int));
         NSWindowListForContext([NSApp contextID], numberOfWindows, listOfWindows);
 
         for (iw=0; iw<numberOfWindows; iw++)
         {
             NSWindow *aWindow=[NSApp windowWithWindowNumber:listOfWindows[iw]];
             // type your code here
         }
 
         free(listOfWindows);        
     }
 }

Note : this code uses a private method to get application context-id needed for NSCountWindowsForContext/NSWindowListForContext functions : **contextID** of NSApplication class.

MrBru.

----

Thanks for pointing me in the right direction MrBru. Unfortunately your solution did not work during a quick test (I'm using PyObjC, and it looks like there is a bug in the signature of NSCountWindowsForContext). After a bit more reading in the NSApplication documentation I found this method:

 orderedWindows - Returns a zero-based array of window objects,
     where the position of a window in the array is based on the
     front-to-back ordering of the window onscreen.

That will do what I'm looking for. Again, thanks for the nudge in the correct direction. -Daniel

----

Maybe I'm doing something wrong, but I'm trying to make an NSWindow and pass an object to it, so it knows how to set itself up. I used NSWindowController to spawn the NSWindow programatically and the delegate was set in Interface Builder. I then wanted to call -window on the NSWindowController which, according to documentation, is supposed to block until the NSWindow is ready. Knowing this, I assumed the delegate would have been allocated by this point. Whether it be the delegate or the owner (which I also tried to set), both objects are nil values. I would think it's simple to pass an object to an NSWindow so help set it up, so I assume I'm just looking at this problem from the wrong angle.
Any ideas, anyone?
- James (JAS)

