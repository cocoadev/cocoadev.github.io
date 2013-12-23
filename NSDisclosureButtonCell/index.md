---
layout: page
title: NSDisclosureButtonCell
---



Please do not use this class. Thanks! Apple.

-----------------------

Posted on the cocoa-dev mailing list by a Gian Luca Cannata:

Hi, all

I have found in AppKit an undocumented class NSDisclosureButtonCell!!!!

With this class is possible to create an Disclosure Button in Jaguar style.
    
NSDisclosureButtonCell *newCell;
NSImageCell *imageCell;

imageCell = [[NSImageCell alloc] init];
newCell = [[NSDisclosureButtonCell alloc] initWithCell:imageCell];
[newCell setTarget:self];
[newCell setAction:@selector(myAction:)];

[_myButton setCell:newCell];
[imageCell release];
[newCell release];

Woooooooooowwwwwwwwwwwwwww!!
----
Apparently, this is only available with 10.2. -- Dustin Voss
----
No, Rickster (of OmniGroup fame) posted about it on MacNN *way* before Jaguar was even announced. � Ibson
----
Not to appear stupid or anything, but what the heck is a Disclosure Button?
-- DaveFayram
----
It looks something like this:  [ \/ ]

--NicholasRiley
----

That's fine and good, but what in general is a disclosure button supposed to do? Like what is the purpose of said button?

-- DaveFayram----
I can't believe you've never seen one - in the Save dialog box, in the keychain  access dialog box, in "Connect to Server" in the Finder???

Do you mean the blue button with an up or down triangle to show or hide the NavigationServices browser?  That's not what this is.  This is the disclosure triangle next to folders in list view in the Finder.

--NicholasRiley

----

Ohhhh.. I didn't realize there was a specific widget just for that. I figured the delegate just changed the button and called the window resizing. I thought it might be that but ruled it out because it doesn't really seem to do anything.

-- DaveFayram

----

Panther will apparently support this more in-full; the button popup allows one to choose whether the button is a disclosure button.

-- AndrewMerenbach

Yup, it's there. �DustinVoss

