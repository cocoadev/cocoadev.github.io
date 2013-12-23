---
layout: page
title: XCodeVersionOneDotTwo
---



General/XCode 1.2 was released with little fanfare late this afternoon (april 2004).

It's a ~640mb download with "Bug fixes and increased stability" being some of the main changes.

----

Excuse my ignorance, please, but how do I sew together all the *.dmgpart.bin files? There's 21 of them.

----

Haven't downloaded it yet, but generally you open the first one and the rest follow.

Well, I'm amazed. I let stuffit uncompress the first .bin file, and double-clicked the dmg. The other dmgpart.bin files I guess were attached and uncompressed behind the scenes.

*You can create these with*     hdiutil segment -o [image name] -segmentCount [# of segments]. *You can also specify*     -segmentSize [size of segment ( in sectors )]. General/DropDMG can also do it.

----

I was impressed by this as well.  Now I know how to backup my large iMovie/iDVD projects!
----
Man I am getting tired of Apple playing catch-up. Anytime you see a **Known Issues** section in a release note that has more than a couple of items and some of those items include application crashes, it just means that it wasn't ready but we are going to release it anyways. Happy beta testing! WTF happened to releasing quality software?

----

Buddy, if you want to bitch and moan about a buggy IDE, then go back to General/RealBasic. Something as massive as major version release of Xcode is bound to have issues. The important thing is that Apple knows about the issues, are communicating the issues, and that they are working to fix them.

Besides, we're developers, and can handle the occasional rough corner on stuff.  Even with the most careful attention to detail, stuff *will* slip through, especially in something as complex as the xcode suite o' tools.

----

1. What's General/RealBasic? Sorry, I am not a high-level type.

*Click the blue underlined words to find out.*

2. Rough corners? Developer tools should work and work well. The development process brings enough rough-corners; I don't want to be fighting crashy tools on top of it.

*Obvously, you're not a high-level type, or you'd be used to it by now. BTW General/XCode 1.2 hasn't crashed once here.*

3. I'm glad your not my GP. ;-)

*I'm sorry your "your" is not your "you're". * Your contractions could use some expansion.
----
Great, now you are reduced to pointing out grammar mistakes. I don't see any contractions in my statements. Why would high-level language users expect a crashy IDE? I expect a rock solid IDE to made available regardless of the developer audience. It's not like it's actually performing most of the work itself.

*Exactly.  You said your, when you meant to say you are, or you're.  And Xcode is doing most of the work itself.  It's editing, managing possible multiple instances of the compiler, linking, copying resources, and many other things all on its own.*
----

Why won't Apple post Release Notes on the Apple Site?

----

If you really want to bitch and moan about things which were released while there were still many obvious bugs, try using Windows XP. I had to reinstall it the other day and within hours several big obvious bugs had appeared (hopefully the 60+ critical updates I installed will have successfully swept some of them under the carpet, leaving only the 'normal' bizarre GUI glitches.) *sigh* I need a Mac job. I'd much rather deal with General/XCode 1.2 than this.

By the way, those of us who have paid ADC memberships will get it on CD, so don't worry about downloading all those segments.
----
Wow! The phrase "Bitch and Moan" was the most used combination of words on this page!

----

Anybody have anything *interesting* to say about the update? New features you can't believe you lived without? Anything?

There aren't really any new features (it's a dot update after all) but everything seems faster and smoother.
----
I really see no obvious difference between 1.1 and 1.2. The only reason that I updated General/XCode, is that it would bug me if I did not.
----
I disagree big time; One of the most annoying bugs for me has been fixed; if you tried to innitiate a build or debug durring a predictive compile the whole build train would hang and you'd need to restart Xcode, which bit the big one.  So I'm glad they fixed that.  Noticably fewer crashes, too, since Xcode has never been stable -- in a way, I'm surprised it crashes so often, given that its progenitor (General/ProjectBuilder) was stable as a rock, if I remember... 
----
Project Builder was most certainly _not_ notably more stable than Xcode 1.0.
----
Can somebody tell me where to download it?
I have the basic (free) ADC membership and it only shows version 1.1 in the Purchase section, and it is 20$ !! (I did not know it was not for free anymore...argh!!)


HOLLY SHIT, GUYS.  XCODE ISN'T FREE ANYMORE.  LOOKS LIKE THEY'RE GUNNA CHARGE US $20 FOR VERY DOT UPDATE.  I downloaded 1.2 through my free ADC connection, but it looks like several days after release they changed it.  DAMN, DAMN, DAMN INDEED!

Calm down, and take a deep breath.  Sometimes the 'download software' link in Developer Connection goes missing when their servers get overloaded.  Wait a couple of hours and try again.  (granted, it'd be nice if they put a note to that effect on the page, but they don't). 

As of 3:47 PM Pacific the downloads are back!
----
Well, The 1.2 update is absent on the May ADC mailing. Any insight on when/if we might see it. June perhaps?

Whenever the next Dev Tools cd is scheduled for, I guess. Just download it.

And don't forget there's a lead time on making General/CDs.  A delay between the download being available (and maybe a patch or two) and getting the General/DevCD is perfectly reasonable.

----

Just found out that due to the 'improved' documentation layout, General/CocoaBrowser is not able to correctly display methods by name...

----

They also "fixed" the shared prefix header cache so that they are no longer shared between projects.  This makes builds much slower and take a lot more drive space than under 1.1.
