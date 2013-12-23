---
layout: page
title: ClosingCustomWindow
---

Alright, I have my custom window all set up and opens up great. I made a little button to be the close button in the top left (In Interface Builder). I cannot seem to get this to close the custom window, performClose does not work and just beeps at you. In the Cocoa docs I see an NSClosableWindowMask. Will this do anything for me?

thanks,
scott

Did you try just using -close?


Well, not really, I was not sure as how to do that.


----

Anyone know?

Like I said above, make the IBAction for your "little button" send -close to the window instead of -performClose:. I think the problem is that -performClose: (as it says in the docs for NSWindow) will make a system beep if the window cannot be closed (for several reasons listed in the docs... go and read them). Sending -close instead of -performClose: bypasses these checks and will forcibly close the window. -- KevinPerry

----

I have tried -close and it does the same.   -scott

Which did you try? Connecting the button directly to the window's -close/-performClose: methods in IB, or connecting the button to your own method in a controller and calling -close/-performClose: in it? If you only tried one, try the other.

----

Alright, it works now. For some reason I had close:nil there. Getting rid of :nil made it work.

-scott

