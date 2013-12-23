---
layout: page
title: DebuggingAutorelease
---



*Extra keywords (for lil' Google): debug, reference-counting, release.*

Update: For Leopard users, I recommend using Instruments: http://www.corbinstreehouse.com/blog/index.php/2007/10/instruments-on-leopard-how-to-debug-those-random-crashes-in-your-cocoa-app/

One of the most opaque bugs I've had to deal with in Cocoa is leaving a released object in the autorelease pool, causing an EXC_BAD_ACCESS in NSPopAutoreleasePool().  When this happens, it's pretty much impossible to tell what the doubly-released object was and where it was instantiated.

Fear no more!  Using Cocoa's NSZombie debugging class and the command-line malloc_history tool, we can nail this bug in a pinch.

Suppose you have the following (obviously incorrect) code:

    
  NSAutoreleasePool* pool = [[NSAutoreleasePool alloc] init];
  NSData* data = [NSData dataWithBytes:"asklaskdxjgr" length:12];

  [data release];
  [pool release];


The dataWithBytes: method sends an autorelease message to the created object, so we don't need to release it ourselves.  When the autorelease pool is tossed the freed data object gets another release message, our app crashes, and we have no idea why.

Here's what we do:

Click on the "Targets" tab, open "Executables" and select the app (In XCode 2.0, double-click the executable in the file tree and select the arguments tab to enter environment variables).  In the executable settings, add the following environment variables and set their values to "YES" (without the quotes):

    
  NSDebugEnabled
  NSZombieEnabled
  MallocStackLogging


You may also want the following environment variable set to YES:

    
  MallocStackLoggingNoCompact


With NSZombieEnabled, Cocoa sets an object's isa pointer to the NSZombie class when its retain count drops to zero instead of deallocating it.  Then when you send a message to an NSZombie object (i.e., you're accessing freed data), it raises an exception and tells you where the object lives:

    
  2003-03-18 13:01:38.644 autoreleasebug[3939] *** *** Selector 'release'
  sent to dealloced instance 0xa4e10 of class NSConcreteData.


Since you have MallocStackLogging turned on, you can now run "malloc_history <pid> <address>" to see the stack trace when the object was allocated:

    
  [dave@host193 Frameworks]$ malloc_history 3939 0xa4e10

  Call [2] [arg=32]: thread_a0000dec |0x1000 | start | _start | main |
  +[NSData dataWithBytes:length:] | NSAllocateObject | object_getIndexedIvars |
  malloc_zone_calloc 


if you run under gdb, you may enter:
    
 (gdb) shell malloc_history 3939 0xa4e10


And there it is: the double-released object was allocated with [NSData dataWithBytes:length:] in the function main()!

I love you, Cocoa!

----

Another useful breakpoint is "szone_error"- this stops the debugger where you get the "Incorrect checksum for freed object" message

----

Also note that NSZombieEnabled keeps objects from being freed, so if you use it with MallocStackLogging you won't see premature releases.  Turn off NSZombieEnabled and wait for the segfault..  Hopefully your debugger will still be awake and can show you the line you're crashing on.

----

What would a malloc_debug like this mean?

    
Call [2] [arg=24]: thread_a000a1ec |0x0 | _dyld_start | _start | main | NSApplicationMain
 | -[NSApplication run] | -[NSApplication sendEvent:] | -[NSWindow sendEvent:]
 | -[NSControl mouseDown:] | -[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:]
 | -[NSCell trackMouse:inRect:ofView:untilMouseUp:] | -[NSCell _sendActionFrom:]
 | -[NSControl sendAction:to:] | -[NSApplication sendAction:to:from:] | -[MEController 
newCity:] | -[MECityEditor editCity:otherCities:] | -[NSApplication runModalForWindow:]
 | -[NSApplication _realDoModalLoop:peek:] | -[NSApplication nextEventMatchingMask:
untilDate:inMode:dequeue:] | _DPSNextEvent | BlockUntilNextEventMatchingListInMode
 | ReceiveNextEventCommon | RunCurrentEventLoopInMode | CFRunLoopRunSpecific
 | __CFRunLoopRun | __CFRunLoopDoObservers | _handleWindowNeedsDisplay | -[NSWindow 
displayIfNeeded] | -[NSView displayIfNeeded] | -[NSView _displayRectIgnoringOpacity:
isVisibleRect:rectIsVisibleRectForView:] | -[NSThemeFrame 
_recursiveDisplayRectIfNeededIgnoringOpacity:isVisibleRect:rectIsVisibleRectForView:
topView:] | -[NSFrameView _recursiveDisplayRectIfNeededIgnoringOpacity:isVisibleRect:
rectIsVisibleRectForView:topView:] | -[NSView _recursiveDisplayRectIfNeededIgnoringOpacity:
isVisibleRect:rectIsVisibleRectForView:topView:] | -[NSView
_recursiveDisplayRectIfNeededIgnoringOpacity:isVisibleRect:rectIsVisibleRectForView:topView:]
 | -[NSView(NSInternal) _getDirtyRects:clippedToRect:count:boundingBox:] | -[NSRegion 
mutableCopy] | NSAllocateObject | _internal_class_createInstanceFromZone | malloc_zone_calloc


My program does this when I select an object in a popupmenu.  When I break at malloc_printf the program breaks inside NSPopAutoreleasePool, so I know I have an autorelease bug.  This is one of several "double free" bugs I've inheritted with code that I'm taking over.

Thanks for any help, Joe

----

Just wanted to say thanks to those who wrote this page up - just saved me hours of sleuthing... Using this tip I pinpointed my autorelease bug in a few seconds - great stuff. However, it also shows one situation where the "rules" for autoreleasing need to be modified, possibly. In my app, I create a small custom window instance that is used to provide feedback info, a bit like a tooltip. Its class includes a class factory method for returning a shared window instance, and following the usual rules, this method was autoreleasing the window object it returned. Users of the window would retain it as normal. However, a window seems to require a - close method when you're done with it, not a direct - release. At app quit time, all windows are sent the -close method, which in this case releases this window, which means that when the original autorelease pool does release this now deallocated object, it crashes. My fix (for now) is to just return the object from the factory method without autoreleasing it, which stops the crash at quit time. I'm a little uneasy about making a special case of not autoreleasing from a factory method, but when the object in question is an NSWindow subclass, that seems to be what you have to do. Unless someone knows otherwise, of course! --GrahamCox

----
"How to do otherwise" would be to uncheck the "Release when closed" box in IB, or     [window setReleasedWhenClosed:NO] in code.

----
Ah yes, that also does the trick - and more cleanly. Thanks! --GC.

----
This is a great article.  However, keep in mind that NSZombieEnabled does not help when dealing with toll free bridged classes, for that you need CFZombie. -- smcbride

----
How do you change environment variables in XCode 2.4?  There is no "Arguments" tab when you inspect the target. - Environment variables are in Project -> Edit Active Executable window (Cmd-Opt-X).

----
Thanks for this page...I've just recently gotten into cocoa & xcode, and this helped me track down some tricky stuff.  - MS

----
This post was the difference for me as far as release or no release.  Thank You, Thank You, Thank You! - IO

----
One thing I find very useful is being able to find an object's retain count in the debugger. One easy way to do it is to define this macro in .gdbinit:

    
define rc
call (int)[$arg0 retainCount]
end


You can then type     rc *object* to see an object's retain count.

----

<Foundation/NSDebug.h>  has some very useful routines on NSAutoreleasePool that might help someone debugging this stuff.  e.g., [NSAutoreleasePool showPools], and related.

peace - hsi

----

This trick is indeed very helpful, but remember to turn it off afterwards. I forgot to do it a while ago and wondered for a while why my app was leaking like crazy. It appears that those "zombies" are considered leaked memory by Instruments.

----

From http://lists.apple.com/archives/Cocoa-dev/2006/Mar/msg00058.html

From: Greg Parker <email@hidden>
Date: Wed, 1 Mar 2006 13:19:56 -0800

This isn't easy on i386, because the exact location changes depending on how far inside objc_msgSend() you got.

This procedure works well, though it's not fully automatic:
1. Run `x/s $ecx`. This should print the name and address of the selector. If it doesn't, you're too far inside objc_msgSend() to get an easy answer.
3. Run `x/8x $esp`. This is the top 8 words of the stack.
4. Look for the selector address (from step 1) in the stack contents. The word just before the selector address is the receiver object's address. The method's other arguments, if any, start after the selector.

If step #1 doesn't work, check whether you're stopped at the very first instruction of objc_msgSend(). If so, step forward one instruction (`si`) and try again. That first instruction is the one that moves the selector into $ecx.

Example:
0x9ff57eef in objc_msgSend ()
(gdb) x/s $ecx
0x9ffcb230 <_errNotSuper+412640>: "sharedSpellChecker"
// The selector is "sharedSpellChecker", and its address is 0x9ffcb230
(gdb) x/8x $esp
0xbfffee34: 0xbfffee88 0x003539b0 0x93624629 0xa34ab0c0
0xbfffee44: 0x9ffcb230 0xbfffef18 0x9ff57f36 0xa34ac480
// The selector address is the 5th word on the stack, so the receiver is 0xa34ab0c0
(gdb) p (char *)object_getClassName(0xa34ab0c0)
$1 = 0x932aa1750 "NSSpellChecker"
// The receiver is either class NSSpellChecker or one of its instances. This case happens to be the class itself, for + [NSSpellChecker sharedSpellChecker]

----

This is also very handy: http://www.sealiesoftware.com/blog/archive/2008/09/22/objc_explain_So_you_crashed_in_objc_msgSend.html
Pour vous joindre   maintenir votre  numéro, vous aurez   comptes   driver ( signal ) [http://obtenir-rio.info numéro rio]. Vous obtiendrez  êtes certain d'obtenir  pour  gratuit  par  entrer en contact avec la voix  expression du serveur ou du service à la clientèle  clientèle   du   entreprise [http://obtenir-rio.info/rio-bouygues numero rio bouygues] . Vous ne  CAN   get un SMS  avec vos . Avec  votre actuelle [http://obtenir-rio.info/rio-orange code rio orange], alors vous pouvez  à l' offre de votre  ON  orange orange .

