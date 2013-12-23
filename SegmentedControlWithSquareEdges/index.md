---
layout: page
title: SegmentedControlWithSquareEdges
---

Does anybody know how to create an NSSegmentedControl with squared edges like in Delicious Library (Details, My Info, Similar)  or iWork's inspectors? I bet the inspectors in iWork are something else, but from what I can tell from Delicious Library, since there is no images for drawing it in the Resources or anywhere else I can find, it is an NSSegmentedControl. Any idea? Thanks, --LoganCollins

----
I haven't checked but I'd bet they're individual buttons. In IB, drop a couple buttons on a window and change the type to "Square Button". Close? If not, you could just subclass NSSegmentedCell and override     drawSegment:inFrame:withView: to get whatever look you're going for. *shrug*

----

I think subclassing is definitely overkill. I achieved this effect by creating a button and using custom images (one for 'normal' and one for 'pressed'). Only thing that sucks is if you want to rearrange them (by moving the icons on the 'end cap' buttons to different positions), you have to create new graphics (sigh, back to PhotoShop).

----
Maybe if you subclassed NSControl and started "from scratch" but not if you start with NSSegmentedCell. Subclassing isn't overkill, using multiple images (and my ram for such) is.

I just did a quick survey of the apps on my system and most use the multiple-images-per-control thing; tiffs in the 16-20KB range. I won't name names but the worst offender on my system has 3.5MB worth of images in its Resources folder, nevermind the frameworks it carries.

Not all of those are being used to make custom controls (I see cursors, icons, etc) but still. Think about the poor souls with *just* 256MB RAM and BringBackTehSnappy.

----

*Oh, please do name names. :-) I know Apple's iApps do this a lot. I do this in my own and my entire app bundle is still 1.5 MB  (with lots of custom button graphics). If done right, it's not going to make that big a difference. The files my app works with are typically 100X the size of the app itself. A drop in the bucket in other words. It's a good argument to at least be *careful* about image quality, etc. It's also a good argument that using a custom image in place of a hand-rolled control will look far prettier (if you're not too good at drawing pretty things in code) and will be a lot less work. Not to argue with myself, someone mentioned elsewhere on this site that "Liger" (10.5) is supposed to be able to scale for large displays, rendering static images ugly. But then again, it's EASIER ... oh, decisions, decisions. At any rate, until Liger was mentioned, this was a perfectly acceptable practice if done sparingly. Your mileage (or opinion, rather) may vary.*

----

FYI, Apple's apps often just use an NSMatrix of NSButtonCells, since they can act pretty much like segmented controls. --BenStiglitz

----

I've done custrom drawing for things like NSPopUpButton, but I could swear Declicious Library doesn't use images to draw the background of its buttons. I have searched through all of its Resources folder and can't find anything, but the images that are set to each segment (i.e. the details 'i' icon and the myInfo star) are in there. I can't figure it out. I don't want to go through subclassing it until I can research it a little more. Thanks to all who have answered so far, and if you guys think of anything else I'd love to know. Heck, I wonder if we were to e-mail MikeMatas and ask him all the questions we want to know about Delicious Library would he tell us? Maybe not. ;) --LoganCollins

----
Wil Shipley is on record (in one of his blog posts, sorry I can't provide a more specific reference) as being very anti-image-files, and very pro-generated-images. So the fact that Delicious Library doesn't have custom images is not surprising given that, it probably generates them at runtime (or just skips images and draws the stuff manually).

----

< RANT > This is somewhat off-topic, but ... since when has Wil Shipley become the Messiah of Cocoa Development Rules? Seriously ... Delicious Library is a huge success for him - kudos - but a lot of people lately have been throwing his name around as if it's a Token of Rightness. "WIL SHIPLEY said ...." (sigh) There's more than one way to solve a problem. Some are better than others in some situations. This *is* a situation where it seems better to stick with code-drawn controls (only because of the advent of Liger and scalability), but just because one "Cocoa Hero" does it one way doesn't mean everyone else should. < /RANT >

----
I personally think Shipley is a jerk and should generally be ignored. I had no intention of saying anything remotely like, "Shipley says this, so that's what you should do". I was merely offering an explanation as to why Delicious Library is the way it is (since he made it).

----
Please don't misunderstand - that wasn't directed at you; just an annoyance. I've seen one too many references to His Holiness lately. :-) Plenty of people like DL, so there must be something to it. Personally I have no use for it whatsoever - I'm not a library and I don't loan out my books and DVDs at such a rate that I need a library management system, nor is my collection of either so large I can't just look at the shelf. Either way, this success has gone straight to his head and some of his ego-trip code examples in his blog are flat-out bad ideas.

----
No offense taken, I just wanted to make sure my attitude was perfectly clear.

I think that his association with Omni, the (exaggerated) success of DL, and the way in which he sounds like he knows everything, all contribute to his following, even though, as you say, a lot of his posts are bad ideas. But so it goes, I think every community has people who sound good but don't always have the best ideas, and Shipley is ours. If he did not exist, we would be forced to create him.

----
Good point. I think this means then, that: Shipley < Dvorak < Satan.

----
Geez, I can't believe you'd get upset that someone mentions me <i>in a post asking how I did something in Delicious Library</i>. In a developer forum these ad hominem attacks seem pretty out of place: you can think I'm a jerk all you want, but how does that bear on this issue? And I don't have an "association" with Omni, I founded it. My success with DL has never been exaggerated by me, and your calling my a liar from behind a curtain of anonymity when you cannot possibly know my finances is an act of cowardice. If you don't like the way I code in my blog you are free to either start your own blog and explain why you feel differently, or ignore me. People write me and ask advice, and I answer to the best of my ability; I don't force anyone to read my blog if they are not interested in what I think. I am trying to give something back to the community by telling developers how I achieved the level of success I have, and your response is to insult me, insult my advice, and question my success. This seems incredibly ironic on a site that is <i>dedicated to sharing information amongst developers</i>. Well, I'm sure glad I tried to share! It was very rewarding. -Wil

----
I don't really know how Delicious Library does it, but it uses NSSegmentedControls as verified by FScript Anywhere. I used the select view button to show me what class it was using. He might be using an undocumented method. The inspector in Pages uses an NSMatrix of NSButtonCells. My suggestion is to just bump up NSButtons together.
--ZacWhite

----

Not to be rude and offer the simple solution, but here is what DL does:  Simply put the segmented control in a view smaller than the segmented control, cutting off the edges.  lol.

----
That's too easy and, at first glance anyway, appears to cut off the focus ring.
----
There is no focus ring of a segmented control.  Remember, DL draws its own indented border -- so it gives the illusion that the segmented control ends there, when in fact it is being cut off.

----
Not to mention the enclosing view could draw its own focus ring ...

----
There is a focus ring for segmented controls, you just need to turn them on...

System Preferences>Keyboard & Mouse>Keyboard Shortcuts

Then select the **All controls** option at the bottom.

Personally, I'd go nuts without being able to tab to any control. When I want that-button-right-there I could take my hands off the keyboard and mouse to it or I could tab twice and hit the space bar. Guess which is usually faster...

----

That sounds perfect, I had thought about it but I was worried about how it would look, but I'll try it. Thanks for the input! --LoganCollins

