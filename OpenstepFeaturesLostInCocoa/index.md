---
layout: page
title: OpenstepFeaturesLostInCocoa
---

[This page contains information extracted from a discussion of Cocoa menu support: NSMenuView]
See also OpenStep

A lot more Openstep capability than mere menu support was lost in the transition from Rhapsody to Mac OS X Cocoa.  Ironically, NeXT kept more Openstep technology intact in Openstep Enterprise running on Windows NT than Apple was able or willing to keep when they ported the Openstep frameworks to Mac OS X and called it Cocoa.  Apple has been slowly recovering capabilities that were jettisoned from Openstep for whatever reason. 


*Driver Development--Developing Objective-C device drivers -Not available in Mac OS X
*D'OLE-Not available in Mac OS X
*EOF (Enterprise Objects Framework)- Not available for Objective-C in Mac OS X but largely replaced with Core Data and KVC
*PDO (Portable Distributed Objects)--Cross platform and multi-ORB distributed objects - Not available in Mac OS X
*NEXTIME --Objective-C time based media support -Finally replaced with QTKit
*WebObjects- No longer available for Objective-C in Mac OS X
*Application Kit - Enhanced in many respects in Mac OS X but missing some features and others degraded
*Foundation Kit - Enhanced in many respects in Mac OS X but missing some features and others degraded
*Display PostScript - Replaced by Display PDF which has more features like built in antialiasing but lost programmable window server and degraded performance for some operations like stroking lots of connected line segments.
*Sound Kit - Not available for Objective-C in Mac OS X; SoundView and similar features have no counterpart in Mac OS X
*Music Kit - Donated to Stanford and basically forgotten
*Indexing Kit - Made Digital Librarian possible, and Digital Librarian is still missing from Mac OS X
*3D Graphics Kit - There is no Objective-C equivalent in Mac OS X, but all features are available from C on Mac OS X.
*Interactive RenderMan and Photorealistic RenderMan - Not bundled with Mac OS X

----

*The Mac OS X finder is in many respects not as polished or functional as the Nextstep/openstep browser.
*Tear off menus, top level services menu, menu hiding, relocatable main menu, vertical main menu orientation, and Objective-C friendly menu API are all lost in Mac OS X.
*Sheets, drawers, child windows, integrated Open GL, Core Graphics, Core Image, and WebKit were all added by Max OS X.

----
Apple would no longer exist if they had not provided Carbon as a first class citizen.  Carbon is a procedural API and ubiquitous features like menu support therefore had to be implemented in a procedural way so that Carbon could use them.  NeXT had no legacy cruft to support and therefore could afford the luxury of a pure Object Oriented interface to things like menus.  Once the unavoidable decision was made to support Carbon, the best Cocoa could hope for was either a parallel object oriented solution or an object oriented wrapper around the procedural APIs.  The pure environment of Openstep was sacrificed to keep Apple in business.

----
*I don't see it so much as what was lost, but what was gained. This business strategy will not be without its EVENTUAL costs in exploitable OS vulnerabilities. We trade the convenience of the Aqua/Carbon/Finder GUI for the hope of staying one step ahead of the malware, now that the worms have arrived. To reiterate, Apple would no longer exist if these compromises had not been made; but having gone down that path, it is incomprehensible to see Apple reinforcing, rather than sloughing off the dependencies on the old Carbon APIs in the name of adding new gadgetry to the basic OS X interface, without sufficient payoff in utility. I just gave UsingResourceForks a re-reading with new eyes.*

*What most needs to be pointed out is that if you are a pro who ever gets tired of writing code for a platform with such a small market share, or if you are beginner perplexed at the barriers to entry on this platform, you should be thinking about how Apple's business decisions may be impacting those frustrations. Most successful OS X developers particularly love and know how to exploit the intricacies of such a small market.*

*By creating a platform that even a dummy can use, and not closing the loopholes that only a dummy can open wide, OS X could without too much effort evolve into a media platform, rather than a competitor in the personal workstation biz.*
----
What does that mean? Care to elaborate?
----
Does Apple have much interest in corporate IT? Or do they primarily cater to iTunes users most of whom can't find their asses with two hands and a Spotlight? We sometimes act like it would be a betrayal to make a platform that the suits could be interested in. Basically much of the stuff that the dummies love is repulsive to corporate IT departments, is it not? It's the legacy of pre-OS X Mac OS that drives so many serious users nuts. Is it not? In the end, academic users who struggle heroically to make the Unix part work for them aren't going to save the platform. The comment goes with the rest of my italicized remarks, and is not a reply to them.

----

It *would* be a betrayal if the OS that came out of this would **not** be useable by iTunes user that can't find their asses with two hands and a Spotlight. Anyway, I really don't know how having a suit-likeable OS (which OS X already is for the most part) ties in with not having features from OpenStep in OS X itself, including any amount of malware protection that I actually didn't know OpenStep had, or cared for (nor I believe it had). -- EmanueleVulcano aka l0ne

----

That is a good point. I'm trying to deal with too many matters at once here. For instance, the suit-likable part has to do with the suspicion that for too many of us, the customer for our software is either *each other*, or else the eponymous user-with-Spotlight trying to find a certain anatomical orifice. If what made the OS usable to such a person *depended* on those features that were retained early on largely for business purposes, that would be another matter. I'm not imputing any malware-defending aspects to Openstep, but critiquing the retained features that are malware vectors in OS X, things that all-too-clearly now have nothing to do with the Cocoa framework or its antecedents, and all-too-obviously are tied to Apple's now-dead predecessor OS. But Apple are married to Finder and its Carbon portions, and to a file system that owes way too much to that dead OS.

