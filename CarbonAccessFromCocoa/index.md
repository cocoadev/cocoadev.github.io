---
layout: page
title: CarbonAccessFromCocoa
---




A discussion of using Carbon from within Cocoa. For example, I would like more control over playing sounds from within my Cocoa app than NSSound provides. Is it just a matter of calling a Carbon function from within my Cocoa .m file? Or do I need to create a Carbon .cpp/.c 'wrapper' to allow access to things like the Sound Manager? 

Thoughts on interfacing the new with the not-so-old...

----

If you want to call Carbon code from your Cocoa application, just add the Carbon framework to your project, and #import <Carbon/Carbon.h>.

----

We've talked about this before when talking about resource manager calls, haven't we? I've said it before and I'll say it again:

Just link against Carbon and make the calls.

Since Objective C builds some object stuff ontop of C there's no reason why you have to 'wrap' other C based API's. This includes the POSIX layer, OpenGL, and other C APIs that aren't part of Cocoa. And Quartz, a C API layer, is available to both Cocoa and Carbon.

Generally, working with QuickDraw and QuickDraw clients (such as QuickTime) requires some special help from Cocoa classes, such as NSQuickDrawView. But many Carbon calls require no special concern.

As an example Apple's Slide Show screen saver module uses QuickTime to open graphic files (it then uses OpenGL for drawing to screen). Aqua Icons uses Carbon's Icon Services to work with icons. I'm sure other examples exist...

-- MikeTrent

----

There are some issues though, like Carbon Events (don't work within NSApplication's eventloop, AFAIK), and so on.

-- FinlayDobbie

----

Apple has a piece of sample code named Cocoa_With_Carbon_or_C++, here is a description: ...sample code that shows how to call Carbon routines and C++ code from within an Objective-C Cocoa program.  The sample displays a window within which, depending upon the radio button selected, clicking the "Say Hello to the World" button will call functions that use Objective-C, C++, or Carbon respectively.  Calling Carbon routines is relatively easy, involving adding Carbon.framework to your project and including Carbon.h in your source code.  Calling C++ code from Objective-C code involves writing C bridge functions to wrap the C++ in a way that enables usage from Objective-C.

You can find this on Apple's developer web site under Sample Code. The date of version 1.0 (the latest?) is 1/2001.

-- JoeZobkiw

----

As of 10.1, calling C++ from Obj-C no longer requires C bridge functions, as Apple has brought back Objective-C++ (see http://developer.apple.com/techpubs/macosx/ReleaseNotes/Objective-C++.html )

-- FinlayDobbie


Apple has a piece of sample code named Cocoa_With_Carbon_or_C++, ...blah, blah...

Tried that, could not get a simple panel to pop up under Cocoa calling Carbon. Included Carbon/Carbon.h and the Carbon framework in the project. What am I missing here? I've created  the app as a cocoa app.

jeffrey_hazelwood@yahoo.com

Let me clarify...the example Cocoa_With_Carbon compiles and works just fine. But, when I try to create my own Cocoa app, include the Carbon framework and make some Carbon calls, doesn't seem to work for me.

Do you do #import <Carbon/Carbon.h>?

-- DavidRemahl

Yes, I do. It's a sound app, so I call the NSSound stop method and the sound stops so I know it's called. Also, if I put in a regular Cocoa alert panel it works just fine, but when calling the Carbon alert panel, it doesn't show up.

I don't really need to call the carbon alert panel, I was just testing with it since that's what their sample app did. 

----

This might be linked to what Finlay said about CarbonEvents not working with NSApp's event loop; alert panels have to interact with the event loop because they basically halt its operation (which is why they're called "modal windows" - they work in a mode of operation wherein everything else is halted until they're dealt with). -- RobRix

----

AndrewStone wrote an article concerning Carbon and Cocoa:

http://www.stone.com/The_Cocoa_Files/Crossing_the_Line.html

