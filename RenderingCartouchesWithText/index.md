---
layout: page
title: RenderingCartouchesWithText
---

Does anybody know how I can get nice little rounded cartouche like boxes with text in them?  You know how the new panther mail has the little round boxes for email addresses, or how, Safari toolbar items get rounded highlights when they're moused over?  How can I draw those and preferable have them as Cells so that I can stick them in Outline views?  Thanks!

----

You may be able to find sample code at developer.apple.com . But the idea is to create a subclass of NSTextFieldCell and override one of the drawing methods (such as drawWithFrame:inView: or drawInteriorWithFrame:inView:) to get the effect you want. When you get working, could you post your solution here?

-- MikeTrent

I thought there already was an option to make text fields round? Which would imply the existence of such in NSTextFieldCell... -- RobRix

That's not quite the same thing -- MikeTrent

Yeah... I tried doing Rounded textField cells and it looks like crap.  It looks like real crap.  That is pretty definately not the solution.

Oops, I misunderstood what you were asking... sorry, my bad! I'll echo Mike's request for sample code, though, when you've got it. -- RobRix

I, the original poster, will echo all your requests for the sample code.  I'm not having any luck -- at least, nothing lightweight.  The alternative, it seems to me, is to do something horrible, and use CG calls to do the drawing... which I really shouldn't have to do...  

Bad news everybody.  drawWithFrame it is.  NSBezierPath is it.  A nice clean call it is not.


I think the way mail does it is with images.  The mail bundle has a png for the left and right sides of the box, and a section of the middle that expands to size.
look at pop-norm-right.png, pop-norm-left.png, pop-norm-mid.png, etc... in the mail bundle.  There also are seperate images for a clicked control.
Not the most high-performance solution, but it makes it easier...

--DanTreiman

Take a gander over here (http://harmless.de/cocoa.html#rollover), someone recreated the safari buttons, it may help.

