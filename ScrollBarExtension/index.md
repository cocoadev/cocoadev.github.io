---
layout: page
title: ScrollBarExtension
---

I'm polishing up the gui for a project I've been working on, and am trying conserve space where available ( to give maximum space to the components which need it more ). So last night, it occurred to me that I could, in principle, put a button *into* the scrollbar area like you see in XCode and MS Word. I'm attaching a screenshot of Word so you can get the picture:

http://home.earthlink.net/~zakariya/images/scrollbar-extra.gif

Now, I know Word's a carbon app, and my app is Cocoa, but given I've seen this done in XCode which I believe is cocoa, it seems this might be possible.

So, for context, I'm doing this to an NSTextView which is being used as a console output; I tried getting a pointer to the NSScroller (successfully) and repositioning it and resizing it such that the bottom would be bumped up 16 pixels. But nothing actually happened. 

I'd attach my faulty code here, but I'm at work and don't have access to my home computer. Sorry.

Anyway, if anybody knows how to approach this, or can otherwise help me, I'd be greatly obliged. Thank you,

--ShamylZakariya

I haven't tried this out, but it may give you some ideas:

http://cocoa.karelia.com/AppKit_Classes/PlacardScrollView__.m

-- PhilLarson

----

Go to: http://examples.oreilly.com/buildcocoa/ and download buildcocoa_examples.sit

The code for GraphPaper in Chapter 19 shows how to do it.

----

Thanks, I'll try those out. It's looking to me that my error was to put the frame resize/reposition code in my awakeFromNib -- where it's only executed once -- instead of overriding the tile method. I had just assumed that the NSScrollers were positioned once and then given following/resizing spring rules, instead of continuous manual positioning via the tile method. In fact, the latter didn't even occur to me. Thanks!

... and now, I just did it during my lunch break. It works! I even twiddled it a little to slip it into the blank area where the horizontal and vertical scrollbars don't overlap. Very cool.

Check it out!
http://home.earthlink.net/~zakariya/images/scrollbar-extra-complete.gif

(Earlier I posted a big screenshot, but then I decided it was unnecessary and immodest)

--ShamylZakariya

