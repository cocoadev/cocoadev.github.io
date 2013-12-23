---
layout: page
title: DWKitAndTheGPL
---

(Moved from DWKit)

----
I don't get the stubborn arrogance of those who demand this page live on. Can one of you explain it to me? What intrinsic value does the flamewar below have to offer the Cocoa community? 

----
It has to do with the fact that someone was allegedly "stealing" something. Whether or not it's true is up for debate, and we want to figure out how much freedom/protection the GPL gives (there have been very few cases where the GPL's validity was tested in court, if any at all). At one point, the author of Acquisition was sending threats to open-source developers who had created a project similar to his. Last time I checked, it's not polite to threaten your competition, simply because they're your competition. Baseless or silly arguments/threats aside, keeping legal threats like that out of the biz, as well as keeping the GPL strong are very important to our community. Hey, if you don't care about the GPL, that's fine, but don't expect to force others to be silent about the issue.

----
Why, then, am I the only one **not** deleting comments from this page, but adding my own opinion that it should be removed? Making the assumption that I don't care about the GPL is rather arrogant in itself. It's **this page** I don't think should remain. This isn't the GPL-wiki and it's certainly not a place for flame wars. If you want to argue the GPL, find an appropriate forum, but this page is **FLAME BAIT**, pure and simple. Please stop removing comments - I'm playing fair by leaving everything else intact, have some guts and do the same.

----

The arguments here seem to revolve around three rough themes, all of which can be quickly debased:

1) "Acquisition uses GPL'd code from LimeWire? and thus cannot be relicensed".

As many have noted, the GPL does not transfer between different programs.  LimeWire's GPL code is run as a process entirely separate from Acquisition.  The two communicate using nothing more esoteric than UNIX pipes.  Just as Xcode need not be open source to use "gcc", Acquisition proper need not be open source to use AcquisitionCore.

2) OK, but "It doesn't matter, since he does not distribute the two "parts" of Acquisition separately"

Quite incorrect.  If you inspect the Acquisition download, you will not find AcquisitionCore.  It is loaded separately and ends up as just another file in the user's file system.  The user can modify, delete, and even fully replace this core with something entirely different and Acquisition will run.  Obviously functionality will be affected, just as if you replaced "gcc" with something different.

3) OK, but Acquisition is useless without the LimeWire core and the "core is also completely useless on its own, especially without documentation."

Acquisition will run, respond to user interaction, interface with iTunes, etc. in the absence of the core, just as Xcode will still run even if you manually delete "gcc".  Acquisition will also continue to work as a fine BitTorrent client (perhaps even a better one since there'll be more spare network bandwidth!).

As for the utility of the naked core, the developers of applications like Cabos and Xfactor, two derivative users of the AcquisitionCore, apparently find it useful.


----


Reverted from a rather rude replacement of all text with, "This page should be deleted.  It contains nothing of value."

In response: Then follow the established rules and etiquette of this community. That is, do not delete the contents as it is rather presumptuous to assume that just because **you** want it deleted, it should be immediately deleted. I *agree* that this page should be deleted, but let's follow the rules and tag it with a     DeleteMe - Flamewar.

Good luck though, for whatever reason, stubborn members of this community seem to want their argument preserved for eternity and keep removing the DeleteMe tag every time someone tries to put it up. I think it has something to do with insecurities, etc. ...

----




David Watanabe used to release DWKit under the GPL, but he doesn't do so anymore.  He has the legal right to do this:  He still owns the copyright on the original code, so he can relicense as he chooses.

*This is not accurate. Acquisition uses GPL'd code from LimeWire and thus cannot be relicensed. Dave is breaking the GPL.* - However DWKit is his code, not from LimeWire.

*It doesn't matter, since he does not distribute the two "parts" of Acquisition separately. Thus, both are still GPL-licensed and not releasing full source code is illegal. To quote the GPL:* "These requirements apply to the modified work as a whole. If identifiable sections of that work are not derived from the Program, and can be reasonably considered independent and separate works in themselves, then this License, and its terms, do not apply to those sections when you distribute them as separate works. But when you distribute the same sections as part of a whole which is a work based on the Program, the distribution of the whole must be on the terms of this License, whose permissions for other licensees extend to the entire whole, and thus to each and every part regardless of who wrote it." (see http://www.gnu.org/licenses/gpl.html).

It may be that Acquisition is breaking the GPL - no one said it wasn't.  All that has been said is that the fact that Dave used to release DWKit under the GPL does not imply that he still has to.  There may be *other* reasons that he has to, like if Acquisition is using both DWKit and code from Limewire.

I don't agree with this interpretation of the GPL.  I'm not an expert, but if the above is correct, it imposes much more than it should.  I think the gist of the quoted portion means that if you release your code in a project with GPL'd code, then your code is also GPL'd since the whole falls under that license.  However, if you distribute your own code, then that is considered a separate work.  You own it.  Therefore, you are free to re-license your own code however you see fit as long as you do not use GPL'd code with it.  I think the above poster confuses the word "parts" to mean binary distributions.  If this is the case, then creating a separate framework would conveniently create another part.  However, I think "parts" refers merely to a more generic parts of the code that do different things.  At least, that's what I hope it means.  Obviously, if the code contains portions of Limewire or another GPL project that he didn't author, then it much also be licensed under the GPL.  Other than that, I don't think it is appropriate that the GPL can enforce that kind of control over someone else's intellectual property.  Again, I'm not an expert and I could be wrong.

*The GPL is very restrictive, requiring *all* modifications to also be released under the license. So indeed, your interpretation is in fact false (don't mean any disrespect). The portion that is the key here is* "But when you distribute the same sections as part of a whole which is a work based on the Program, the distribution of the whole must be on the terms of this License, whose permissions for other licensees extend to the entire whole, and thus to each and every part regardless of who wrote it." *Note that since Dave no longer releases the code for "DWKit" (or the code for *any* of Acquisition's interface), nor does he distribute them separately, he is in direct violation.*

----

*He doesn't link against it in any way, AFAIK he runs the Limewire stuff in its own process and therefore it doesn't really count. The problem is that nobody has ever tested what constitutes a derivative work in the case of the GPL, AFAIK, and the rule of thumb which may or may not be legally sound is whether you actually link or not. For example, Xcode runs gcc and gdb in their own processes and communicates with pipes, much like what Acq does with limewire.*

I think the issue with Xcode is that the pieces are distributed separately. Dave has made extensive changes to the actual Limewire code simply to make it work with Acquisition. The Limewire-based "core" process is included *with* Acquisition, and neither piece is available separately. Thus, though they are in different processes, they are virtually the same product. Xcode works similar to Terminal in that it sends messages to other programs which are not included in the Xcode bundle itself. It's true that this issue is not entirely clear-cut. However, I personally think that he does break the GPL. I have dealt with him on this issue personally (in ways which I may not be entirely proud of) but in the end it always seems to be a violation to me. --RobinHP

----

http://acquisitionx.org The core seems to be available to me...

*According to the FSF, if the UI is linked to the limewire code, it has to be released.  Not just the core.*

But it isn't. It is piped, piping is not linking.

*Xcode isn't available without GCC/GDB and would be fairly useless without them.*

But the "core" is included *inside the Acquisition distribution.* The core is also completely useless on its own, especially without documentation. Also, he tends to not keep the core on the site up to date. It is also incomplete and does not compile straight out of the distro. If what Dave does here is legal and acceptable, it is a fairly big hole in the GPL which essentially allows you to break it.

----

This entire page is so full of irony. You are nitpicking over whether a program whose primary purpose is to acquire illegal software/media is breaking the GPL. Of course there are valid uses for Gnutella file sharing, but 99.9999999% of the users of Acquisition are using it to acquire things illegally and breaking license agreements.

*
A significant portion of the GPL community subscribes to the philosophy of "what's yours is ours, what's mine is mine". It's standard behaviour really. Watch children. They hate to share, but will cry if somebody doesn't share with them.
*

----

Oh and another thing, you know when you start Acquisition up the first time and you see the dialogue titled "Downloading Core Components"? Well that is just it, it is distributed separately.

I don't understand why so many of you rush to defend Dave here. It's pretty simple: Acquisition is useless without "AcquisitionCore" (which is not available separately). AcquisitionCore is useless without Acquisition (which is not available separately). I don't see what's so hard to grasp about this. Just because he's gone to elaborate lengths to "legally" break the GPL doesn't make it right. Just because many people use the software to do illegal things doesn't make the (obvious, if not by law, at least in spirit) GPL infringement any better. Also, have any of you tried to have a conversation with him? I will not resort to insults here, but if you have, you would not be in as big a rush to defend him. -- FC

----

It isn't a matter of "right" or "wrong" because those are different for each person. What he has done is legal, simple and no question about it.

----

Obviously, there is a question, or this page wouldn't exist. Many people have provided various reasons why they believe there is an infringement. Please don't bring the "relative morality" issue into play here. True, everyone has their own morals, but that doesn't mean breaking the law is socially acceptable. Not to compare apples and oranges, but just because someone thinks murder is "right," do you think they should be allowed to do it? I don't understand the need to brush away this argument. At least take the time to respond to the points made. -- FC

