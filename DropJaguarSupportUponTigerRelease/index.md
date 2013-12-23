---
layout: page
title: DropJaguarSupportUponTigerRelease
---



Will anyone be dropping Jaguar support for their applications once Tiger is released into the wild? I'm very much considering this since it seems like a good place to do so. Then I can finally use all those Panther-only UI elements :)

----

There are still people supporting Jaguar? ;)

----

Usually we try to keep compatibility in new versions for six more months after the release of a new OS.

*Umm...so Jaguar's already out, compatibility-wise?*

Yes. I am of the opinion that 6 months is enough time to migrate, and they are welcome to use older compatible versions (which we always keep around just for them).

----

JaguarOrPanther has a (months old) discussion with some (months old) usage statistics. J**'agWire users currently stand at 6.7%

----

Drop compatibility if you need to drop compatibility. Otherwise don't. Why change something that works into something that doesn't just because you can?

*Because there are things you can do that require Panther. Bindings, drop shadows, etc. If I can switch to using bindings and chop out a few hundred lines of code, it's worth it, even if the user-level functionality remains the same.*

But if you're already Jaguar-compatible, this means it will take **more** work for you to break compatibility than to stay compatible. If you're doing a major overhaul and the cost of keeping the code you have becomes too much, that's one thing, but chopping out code you've already written saves you zero time. It may give you warm fuzzies, but in the end, is it really worth the trouble just to get bindings (which do have difficulties of their own that the code you already have probably doesn't) and lose a percentage of users greater than the Mac's penetration in the overall PC market? Maybe it's worth it. But probably not. Just something to think about.

*Code requires maintenance, and harbors bugs. Removing code will reduce both, and can be less work in the long term.*

----

While that is generally true, changing code is more error prone than using code.  If you are trying to update or fix a piece of code which you can replace with a more concise implementation if you use newer technologies, then you should.  However, going back and changing already-tested code just because you can seems like a recipe for disaster.

----

*going back and changing [...] seems like a recipe for disaster*

lol... yes, 17 people injured and 3 died last time I did code maintenance/cleanup/refactoring.

Seriously though, my application requires Panther, but I'm looking forward to the day where it'll be Tiger+ -- I have lots of Panther workarounds and my own implementation of several Tiger features. Removing the former keeps the code-complexity down, removing the latter does the same and ensures consistency with the OS.

Code complexity is the main problem when developing big applications, and too much complexity means less flexibility when it comes to changing the code to new requirements. I'd love to drop all my custom outline view controller code for NSTreeController, use CoreData for consistent storage, free undo a.s.o.

Not to mention, being Panther compatible means I need to test each new feature on Panther, since there may be bugs (with Panther) causing another outcome than on Tiger, which has really become a pain now that my Xcode project has been updated to 2.0, which isn't available for Panther (so basically I can no longer build on the test machine).

----

"Not to mention, being Panther compatible means I need to test each new feature on Panther..."

Surely if you use the appropriate SDK this will be a mimimal issue....the compiler will prevent you including any 10.3 incompatible code and you can do the same in IB.  It should then be a case of simply running the app a couple of times before release for QA purposes...

JKP

In theory, yes, in practice, no. SDKs do help a great deal, but they are far from being able to catch everything. The SDKs have bugs/ommissions, indirect method calls won't be caught, OSes have different bugs and different responses for bad inputs, etc.

*Yes, it was mainly the bugs (needing workarounds) in Panther, I was referring to. Some bugs stand out immediately, others are very subtle.*

