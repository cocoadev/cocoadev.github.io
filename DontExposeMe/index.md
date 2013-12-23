---
layout: page
title: DontExposeMe
---



A handful of programs are immune to Expos�, such as the Dock, B<nowiki/>ezelUI, and Konfabulator. However, there is no mention on Apple's site of how to exclude a program (or window) from Expos�. The trick relies on some undocumented CoreGraphics functions. You can accomplish this with a call to <code>[self setSticky:YES];</code>, once you've made a subclass of NSWindow which implements the following code:

    typedef const void *CGSConnectionID; /* CGSConnectionID is pointer-width, not always 32-bit */
extern CGSConnectionID _CGSDefaultConnection();
extern OSStatus CGSGetWindowTags(const CGSConnectionID cid, const CGWindowID wid, int *tags, int thirtyTwo);
extern OSStatus CGSSetWindowTags(const CGSConnectionID cid, const CGWindowID wid, int *tags, int thirtyTwo);
extern OSStatus CGSClearWindowTags(const CGSConnectionID cid, const CGWindowID wid, int *tags, int thirtyTwo);

- (void) setSticky:(BOOL)flag {
	CGSConnectionID cid;
	CGWindowID wid;
	
	wid = [self windowNumber];
	cid = _CGSDefaultConnection();
	int tags[2] = { 0, 0 };
	
	if (!CGSGetWindowTags(cid, wid, tags, 32)) {
		if (flag) {
			tags[0] = tags[0] | 0x00000800;
		} else {
			tags[0] = tags[0] & ~0x00000800;
		}
		CGSSetWindowTags(cid, wid, tags, 32);
	}
}

(Code updated 09 August 2006 from Growl changeset r1906 commited by ingmarstein. Based on original code from this page, but works with Tiger and F11.)

(Code updated 29 June 2008 by JonathanGrynspan for compatibility with 64-bit Mac OS X Leopard. 32-bit Mac OS X and pre-Leopard versions would all be unaffected by these changes.)

You'll need to <code>#include <ApplicationServices/ApplicationServices.h></code> and make sure that the ApplicationServices.framework is linked to your project. If you're having trouble (specifically, if winNumber is 0xffffffff), try unchecking the Deferred value for your window.

*References*
http://www.cocoabuilder.com/archive/message/cocoa/2004/2/2/95943
http://www.cocoabuilder.com/archive/message/2004/2/12/96974

-- RyanGovostes


--------

I've done all that and its still not working? :-/ I am using an NSBorderLessWindowMask window... I added the code to my NSWindow subclass, added ApplicationServices.framework to Linked Frameworks in my project, added the include but still expose moves my window around :-( winNumber != 0xffffffff either... and deferred YES vs NO doesn't make any difference. Help?

*As a side note, NSPanel<nowiki/>s aren't Expos�'d at all -- they just disappear when you click the Expos� key. I think it has something to do with the Window menu -- NSPanel<nowiki/>s don't show up there either. Maybe if you can figure out how to create a window that's not part of the window menu... --JediKnil*

----

Make sure your call to <code>[self setSticky:YES];</code> is after the window is attached to the window server. (i.e. not it the init method of your subclass)

----
But, is there any way to temporary disable Expos� in an event loop (e.g. when you are tracking mouse movement). Right now the user can screw up everything just by pressing any of the Expos� keys.

    
<do something to disable Expos�>
while(loop) { theEvent = [window nextEventMatchingMask:(NSLeftMouseDraggedMask | NSLeftMouseUpMask)]; ...}
<do something to enable Expos�>


-- JP

----

I don't think there is a way to stop Expos� altogether, though you could try freezing the Dock (which is responsible for the Expos� feature). <code>killall -STOP Dock</code> will do this, but you'd have to use a different method to resume regular operation. -- RyanGovostes

----

Evil++?

-- JP

----

What?

----

Of course..  Obviously it's just *Evil+1*.  Which I wouldn't think is too much worse than just Evil alone.  After all, they are still the same *order* of eveil - O(Evil) = O(Evil++).  Of course, *Eveil*=2*, or worse yet *Evil^=n* *[what's wrong with Evil = Evil XOR N????]* **is** is much worse... Kind of like it being as hot as the proverbial eight devils, instead of the more customary seven..  Which in most cases is bad, with a **possible** exception that I won't mention here, because little kids might read this board too :)

----
Killing the dock is evil++, that shouldn't be done in normal operation. *Though that's technically not killing the Dock. It's just temporarily stopping it. But it's still a nasty hack that should not be done.*

----

What about this... 
I want Expos�. I have disabled the dock because with a 12" laptop, losing 1/2 an inch is too much to lose (Really, not evil). I disabled it with an autostart "open & quit" the dock applescript, because the dock needs to run for a moment to enable the desktop. When the dock is quit it disables Expos�. It also disables the "command-tab" application switching, but LiteSwitch was a decent replacement. I also found a new found reliance on Quicksilver, it is really maturing.
Any suggestions? -Kirk

*Is setting the Dock to only show itself when you hit the bottom of the screen with your mouse cursor too annoying?*

Actually yes. It's not essential by any means, but if it's possible I want to disable it.
Many times I go to select an option near the bottom or sides, and instead of adjusting the option I've just opened another app.
**And if nothing else, It's a challenge.** - Kirk

*Since Expose is in the dock, I don't think you can have it without running Dock.app. Your best bet is probably to APE Dock.app to disable the Dock window while still allowing the other functionality.*

Can you direct me/tell me how I might do that? I'm learning... -Kirk

*See the ApplicationEnhancer page (APE stands for APplication Enhancer). Be careful; APE is the bandsaw of OS X programming; it can do anything, but it will just as easily cut your arm off as carve up that wood you're working on.*

Or you could make the the Dock really, really tiny. I believe there is already a program that does that, but I can't find a link. --TheEllmist

*Or you could put the Dock at the top of the screen (under the menu bar). This can be done with a few freeware or shareware programs, such as T<nowiki/>inkerTool. The dock will only come up when you point directly below the menu bar. On the other hand, this has nothing to do with Expos�.*

----

I really would like to figure out how to temporarily disable Expose for the purposes of my app "iTunesMouseRemote," besides killall -STOP and -CONT (which, besides being Evil+1 also causes strange behavior after the Dock is unfrozen). I found that changing the plist file (I forget the name right this instant) did NOT work. Any suggestions would be appreciated. --RobinHP

*did the code at the top of this page not work?*

No, it didn't. The code at the top doesn't disable Expose, it disables a certain window from being "Exposed." I need something to disable Expose from activating when the mouse buttons are pressed.

*That is kind of evil, since it's changing the user's settings.*

Not exactly. The user runs the program with the knowledge that she is resigning all control over the operating system (at least via mouse). Thus, it would be desirable to make Expose disable while the program is running. Games do this very thing. I don't see how it's evil at all. Yes, using killall *is* evil, but I don't *want* to do that.

----

This keeps my window from being Expos�d, but the window doesn't disappear the way Utility Panels do. Worse yet, it actually covers up other windows. Is there a way to make windows fade away during Expos�?

Additionally, are you sure it's supposed to work with <code>setSticky:YES</code>? For me it only works with <code>setSticky:NO</code>.

----

This "sticky" feature doesn't seem to affect Expose for me.  Rather, it affects the "desktop expose" feature, where instead of being moved off screen, sticky windows remain where they are.  I've found the CGSTagExposeFade will cause windows to fade when Expose is activated.  Unfortunately, their windows still take up space in the Expose view even though they aren't visible.  Furthermore, the highlight window Mac uses will appear when the mouse is over the location where the windows are supposed to be.  --KarlGyllstrom

----

Odd, for me it's just the opposite: When I hit F11 my full-screen window is moved aside just like all others, but when I setSticky: NO I get a beep when I try to use F10 or F11 to show me all windows. (This is with an NSPanel, though I also tried an NSWindow - under 10.4.5).

----

There is an Apple-sanctioned Carbon API to remove a window from Expos�: HIWindowChangeAvailability. It takes an HIWindowRef, which is the same as a WindowRef (it's typedef'd) and a WindowRef for a NSWindow can be retrieved via its windowRef method:

    

HIWindowChangeAvailability((HIWindowRef) [myNSWindow windowRef], kHIWindowExposeHidden, 0);



Documentation at http://developer.apple.com/documentation/Carbon/Reference/Window_Manager/Reference/reference.html#//apple_ref/c/func/HIWindowChangeAvailability

----

Too bad that doesn't work for 64-bit builds since it's part of the Carbon UI.

----
    

HIWindowChangeAvailability((HIWindowRef) [myNSWindow windowRef], kHIWindowExposeHidden, 0);


doesn't change anything for me. How can this be used to make a window not react to expose, meaning, it will stay where it is, come rain or come shine?

----

It's worth noting that not all NSWindows will return non-NULL when you send them a -windowRef message. (That is, some don't appear to have WindowRef values backing them.) This may be why HIWindowChangeAvailability() is failing for you. Have you checked its return value, or that of your -windowRef message?

----

Halo achieves the anti-expose and anti-tab mode by taking control of the keyboard input. I guess your app could intercept the keystrokes, bar the F11 ect, and pass the rest through the responder and event chains. I think the control of the keyboard input comes along with the full screen calls of CoreGraphics, because when Halo has the "play in window" option off - the system doesn't respond to various Hot-Keys, when "play in window" is on - the system does. BTW: This is only with the Universal binary versions of Halo, the PPC version has a different behaviour all together.

----

That's not a very good approach, since you can activate Expose via the mouse as well as the keyboard.

----
Halo and other games achieve this effect by use of CGCaptureAllDisplays. It should be fairly obvious that this is not a useful technique for an app that doesn't take over the screen.

----
some of you might want to check-out this page:

http://developer.apple.com/technotes/tn2002/tn2062.html

not particulairly 'DontExposeMe' related, but has some nice 'non-evil' methods of disabling dock/expose and related stuff.

what you're looking for is this:
     OSStatus SetSystemUIMode( SystemUIMode inMode, SystemUIOptions inOptions); 

----
This hack seems to not work anymore on 10.5 Leopard.

----
By "This hack" are you referring to the aforementioned 'non-evil' methods of disabling the dock, or the sticky bit that is the overall topic of this page?
If you are referring to the dock disabling, can anyone confirm that the setSticky method is forward compatible with Leopard?

----
I can confirm that it is not compatible with Leopard.

----
If anyone has any information that would allow us to find a Leopard-compatible means of achieving this, I believe it would be a welcome asset to this wiki

----
The "non-evil" method listed above to disable Expos� - SetSystemUIMode - doesn't actually disable Expos� in any of its states, I believe.  It really should, but doesn't.

----
This definitely works on Leopard, but the window's level has to be kCGDesktopWindowLevel.

----
for me, [leopard 10.5.8] the window is not exposed in expos� by just setting the window level to "NSFloatingWindowLevel".
    
[window setLevel:NSFloatingWindowLevel];


----
The following command makes your window ignored by Expos�, during show desktop it stays in place and during Expos� all windows and similar, it stays in the background:
    
[self setCollectionBehavior:NSWindowCollectionBehaviorStationary];

It goes well with NSWindowCollectionBehaviorCanJoinAllSpaces or NSWindowCollectionBehaviorMoveToActiveSpace, which can be chained on to the previous code with boolean operators like so:
    
[self setCollectionBehavior:NSWindowCollectionBehaviorStationary|NSWindowCollectionBehaviorMoveToActiveSpace];

More collection behaviors are available in the NSWindow docs under the section NSWindowSharingType.

-- BillyDoyle

