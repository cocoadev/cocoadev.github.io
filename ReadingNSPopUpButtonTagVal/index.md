---
layout: page
title: ReadingNSPopUpButtonTagVal
---

In the action for an NSPopUpButton, I do this:

    
fractType = sender selectedCell] tag];


...but I need to also read the  val in a non-action function, drawRect.  I tried this in there, 

    
typehold = [[popUp selectedCell] tag];


..."popUp" being the outlet name for that [[NSPopUpButton.  It keeps returning 0.0000 as tag.  How do I red this tag this way, not in the action function??

Thanks.


blakespot

----

You don't want -selectedCell, because you're dealing with a menu. Try -selectedItem.

-- MikeAsh

----

You give this advice knowing that the first codesnippet, selectedCell in the IBAction function that is tied to the NSPopUpButton, works fine, right?  

blakespot

----

Yes, I did read and understand that. Did you read the documentation on the relevant methods? To quote from NSControl's -selectedCell docs:

*Subclasses of NSControl that manage multiple cells (such as NSMatrix and NSForm) override this method to return the cell selected by users.*

Given that NSPopUpButton isn't mentioned, nor does it have its own documentation for this method, I must conclude that it was working mostly through coincidence.

You are looking for the tag of selected menu item. Menu items are not cells. Do what you mean. Did you actually try my advice before posting your response?

-- MikeAsh

----

Thanks for the clarification.  I did not try your advice before posting, only because I have not had a chance to sit back down with my code in the meanwhile and felt that I may not have expressed myself clearly in the initial description.  Thanks again - I will try the code tonight.

blakespot

----

Roger that, sorry for any, um, tone in my post above. Please do let me know how it works, since I didn't actually *try* it.... -- MikeAsh

