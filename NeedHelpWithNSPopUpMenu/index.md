---
layout: page
title: NeedHelpWithNSPopUpMenu
---

I would like to implement a pop up menu (like when you control+click in Explorer and a list of choices pops up to give you the following options - Internet Explorer Help, Back, Refresh Page, blah, blah, blah, nil ;-)

I like using these tiny windows (that only include a list and no title bar) in other apps and I would like to use them in apps I write.

I already know how to capture the event when modifier flags change due to a control key being pressed ([theEvent modifierFlag]==262144) and I can also capture a mouse down event in the desired view without any problems. I even tried creating an NSPopUpButton in IB and connecting it to an existing controller object and could verify its existance by logging the description of the outlet durring a control+click event. The NSPopUpButton was set with an NSMenu filled with NSMenuItems but when I add the NSPopUpButton to the desired view:

    [myView addSubview:myPopUpButton];
    [myPopUpButton setNeedsDisplay:YES];

nothing happens.......

I've been trying different things for about half a day now. So, instead of racking by brain over this for another four hours I thought I would try a post here. I'm willing to edit and post a complete HowTo on the subject once I figure it out.  

Hope someone can help out with this one.

Ben Um

----

I believe what you really wanted to do was create an NSMenu and set it as the contextual menu outlet for myView. 

Do not blank this out again, might be useful if somone has similiar questions.

