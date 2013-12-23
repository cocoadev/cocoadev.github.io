---
layout: page
title: NewiTunesInterface
---


Anyone have any idea on how to create or modify the unified toolbar to look like the toolbar in iTunes5??
And creating the dark "bar" at the bottom of iTunes?

----

Word. http://www.rogueamoeba.com/utm/posts/Article/polished-metal-2005-09-10-10-00.html

----

GAH! Custom non-system-theme-compatible interfaces are the devil's spawn, and why anybody would want that horrid bastardized version of unified toolbars from iTunes 5 is beyond me. Just use the original, unified, and get it over with. Much better.

*Okay, that's one against ...*

And here is two against.

*Alright, so you don't like it. So lots of people don't like it. But lots of people DO like it ... so, just like drawers and unified toolbars and the metal look in general, it's here to stay. So moving right along, the article above seems to have gotten it mostly right (some dispute over the corners). Yay for them. I'm not anxious to rush right over to the one metalized app I have (which involves interfacing with audio hardware, so zealots begone, please) and jump on the bandwagon. Maybe someday if it becomes 'a standard', but not today. *I* think it looks good; better than the brushed metal look ...*

Eh.  Polished metal is passe.  Flower Power is the new hotness.  http://www.gusmueller.com/blog/archives/2005/9/8.html#1323

*Heh heh ... my kinda' guy.*

<tongue-in-cheek>what is this, CocoaDev or the boardroom at Armani?</tongue-in-cheek>

----

Quoting above: *so, just like drawers and unified toolbars and the metal look in general, it's here to stay*.

We don't know that yet.  Apple experiments, and not every experiment becomes a standard.

*With as many people as I've seen inquiring how to achieve this effect (and the blog entry referenced above), yes, we most certainly do.*

Well, then they should stop and think.  Apple has *not* said that this is something that anyone else should be using.  And they *have* said that they experiment with stuff before putting it in the UI guidelines (or deciding not to).  The only reason that the Mac OS has a look and feel at all is that developers (so far) mostly listen to what Apple says.

There is exactly one app that uses polished metal right now.  There will be no expectation of polished metal on the part of users unless third party developers begin adopting it. (Or unless other Apple apps start using it, or it goes into the HIG or something.)

----

It should be obvious by now that whatever Apple convinces the world it wants in an interface is what some of us will be extremely busy striving to include. Because if somebody convinces the world it wants something, then there's a buck to be made.

Making money is not wrong. But at the same time, you become a yo-yo, the property of the Apple design wizards. Between this and the next craze to clone someone's luscious interface (you know who we're talking about here) it's a wonder we get any work done. It's not the interface that impresses me about iTunes, but the DSP.

*So does that mean you're for or against adopting this interface in third-party apps?* :-)

**Right, so the issue here is that Apple hasn't yet convinced users that this is something they expect.  It's in *one* app.  You'd be just as well off doing your own totally unique thing.**

SOLD: http://gui.interacto.net/

----

*So does that mean you're for or against adopting this interface in third-party apps?* :-)

What I regard so cynically, and what pisses me off so much, is that you cannot even experiment with this without moving to Tiger. Another excellent way to sell boxes of OS (with its box of Xcode) if the pipeline is starting to trickle a bit. There's a lot better stuff they could use if they wanted to sell a few more copies. Of course, the reflex action of most of you is to say, "WHAT!? You mean you haven't migrated to Tiger yet? What sort of lame-a$$ developer ARE you anyway. You GOTTA stay current!!!" In other words, be a yo-yo for Apple, while we wiser developers focus on functionality.

Or, why don't you just subclass NSToolbar and NSWindow and add the functionality which apple payed its employees to add to their latest product upgrade? JeremyJurksztowicz

I actually made the new interface without subclassing anything, hell you can do the unified toolbar without subclassing anything. Not hard. 

*Dan - could you post examples for the subclassless unified toolbar? That'd be very handy to Panther'ists.*

----

Perhaps, however I tend to hate the unified toolbar, I may be doing an un justice to the world ;) .

Basically the steps are the following;

1) Create unified texture that is the height of the toolbar.

2) -setBackgroundColor: the on the window, you need to do this as a textured window.

3) use the hidden unsupported method setRoundedCorner: (iirc) to NO.

4) Either have something that takes up the entire content view OR have a custom NSView as the contentView that simply draws http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSColor.html#//apple_ref/doc/uid/20000353-BCIFCIEG to everywhere.

----

Back again, finished it, it has one known bug where if you hide the toolbar there is no separator line. But I don't feel like fixing that.

Shot:
http://catsdorule.torpedobird.com/tmp/UnifiedPanther.png (No longer accessible at this address.)

Project:
http://catsdorule.torpedobird.com/tmp/UnifiedPanther.zip (No longer accessible at this address.)

----

