---
layout: page
title: NSTextViewAndNSOutlineView
---

Hello,

If anyone has any ideas on how to do the following, I would be very grateful - it has me stumped right now:

I want to rig up an outline view and a text view so that the outline view can be used to rearrange blocks of text in the text view. To try to explain what I mean, I'll give an example: for instance, my text view might read like this:

    
Part 1: Start
Some text here.

Chapter 1: Some chapter
The beginning of a chapter about something right here.

Chapter 2: Second chapter
More text here.


In which case, my outline view would look like this:

    
>Part 1:Start
 >Chapter 1: Some chapter
 >Chapter 2: Second chapter


If I then dragged the items in my outline view around so that it now looked like this:

    
>Part 1:Start
 >Chapter 2: Second chapter
 >Chapter 1: Some chapter


this would automatically rearrange the text in my text view so that it now read like this:

    
Part 1: Start
Some text here.

Chapter 2: Second chapter
More text here.

Chapter 1: Some chapter
The beginning of a chapter about something right here.


I've had a couple of ideas on how to approach this, but nothing really satisfactory. For instance, Jer's Novel Writer (www.jerssoftwarehut.com) does something similar by using a different text view for each block of text, or so it would seem. But this means that you can't use Cmd-A to select all the text, and I also don't like the idea of using so many text views. I also thought of using a custom attribute in my text view and applying it to the headers (eg. "Part 1" could be assigned a custom HeaderPartAttribute or some such) and then subclassing the text storage to keep track of which text belongs to which heading and so on, which could also be used to keep the outline view in sync. However, this is far too expensive for long chunks of text - I tested it out very roughly and things got slow fast.

The most elegant handling of this sort of thing that I have seen is in the outline mode of Mellel (www.redlers.com) on the Mac or Sophocles on the PC. (Mellel uses a custom outline view though.)

Does anybody have any ideas on how I might begin to approach this? Many thanks for any thoughts,
KB

----

I would write a custom NSTextStorage subclass which internally stores every section's text in a different object, but when requests are made through its normal NSTextStorage interface, it does the calculations and pretends that it's one big long block of text. You can then write custom methods to get at the backing store with the separate pieces.  This way, it will JustWork as the text storage of an NSTextView, presenting what the NSTextView and the users will expect, while still logically separating everything into pieces internally and allowing you to get at those pieces individually.

