---
layout: page
title: WindowOrdering
---

I've been messing around with orderWindow:relativeTo: and orderBack and orderFront and makeKeyAndOrderFront and I just can't get the behavior I'm looking for.

What I want to have happen is this:

My app has a main window.  When an incoming message is received I'd like it to jump in front of the main window.  When the application is hidden I would like for the message window to jump in front of ALL windows - that is, in front of the active application.  I don't care if it becomes key or not.

Thanks for any and all help,
Phil

----

I do not know how to get the behaviour you are seeking; I will merely say that from that description, I would under no circumstances using your app. Perhaps you should rethink this behaviour? -- General/RobRix
----
And you would not likely ever have to.  ;-)  It is for our small company's internal  use only.  My boss wanted an incoming message to get the user's attention immediately.  If you can suggest another way of achieving this I'd be willing to give that a try too.  Bouncing icon in the dock?  (anyone know how to do that?)

Thanks,
Phil
----

Dock bouncing seems like a very good bet; it is attention-grabbing and attention-keeping, but it doesn't interfere (much) with what the user is doing at that exact moment. Fortunately, I was able to find the information on this one:

http://developer.apple.com/techpubs/macosx/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSApplication.html#BCICGJJI

Notable are the methods -requestUserAttention: and cancelUserAttentionRequest:, and the documentation on the constants used by them.

-- General/RobRix
----
Thanks a lot!  I'll dig into this...
-Phil
----
I think this is the behavior I need to implement.  I don't know, however, how to obtain a reference to the running application, to act as a receiver for the method requestUserAttention: .

Can you help, General/RobRix?

Thanks, Phil
----

I can't help General/RobRix, but I might be able to help *you*, Phil :) Make sure you're #import'ing <Cocoa/Cocoa.h>, and just send messages to General/NSApp.

See General/AppKitTOC for two links to the General/AppKit API reference; these references are absolutely priceless tools, make sure you use them!

-- General/RobRix
----
Oops, forgot the comma  ;-)   Of course if you can't help yourself...

Thanks.  Works great.  Much less intrusive, yet still attention grabbing.

-Phil
----
