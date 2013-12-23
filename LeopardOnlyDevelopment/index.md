---
layout: page
title: LeopardOnlyDevelopment
---



I'm currently developing an application that will more likely be released *after* Leopard becomes available. So I wondering if I should stick to support Tiger or get an ADC Select membership and concentrate on Leopard only? What would you guys suggest?

----
If I were you I'd read up on some of the features of Objective-C 2.0, CoreAnimation, and other Leopard-only technologies. The main features of Objective-C 2.0 include properties, garbage collection, and a foreach loop. All of these are enhancements that I would not consider useful enough to prohibit use of your application to Leopard users only. CoreAnimation is supposedly a very good feature, although I myself have not played around with it. It is the reason many apps like Delicious Library 2 will be Leopard only. If you plan on CoreAnimation being necessary to your app, then you might consider using Leopard only. --ACoolie

----
Remember, also, that there are still people using Panther and even Jaguar, and that at first Leopard may or may not have many adopters. (I see a lot of people sticking with Tiger for at least a dozen reasons: waiting for a .1 release, unwilling to upgrade out of personal preference or expense, server software with delicate compatibilities, etc.) The farther back you can release your app, the wider your user base. On the other hand, that doesn't mean bend over backwards to make it work on, say, v10.1, but if you can make a minimal effort and link to an earlier SDK, you've got a pretty big distribution advantage over Leopard-only applications. Even so, you may decide to do something like "Requires v10.4, recommended v10.5" and admit that it may not work as well on earlier systems.

But no, a new system is not usually so much better that it's worth dumping an entire user base. For me the line seems to come down at v10.3 during Tiger's release, though, since I like bindings over glue code. That may slide up for me when a new system comes out, but I try to support the previous system as well as the current one. --JediKnil

----

AllanOdgaard is going Leopard-only for TextMate 2; as I recall, his arguments for the move included the unusually large volume of new frameworks and services that would be useful for his app.

As JediKnil noted, you have to weigh the cost of development (for example, how long it will take you to implement feature *x* with a new framework/class vs. the facilities of a previous release) against how many users are actually going to be on the new platform.

For myself, I'm torn. Leopard has a lot of functionality that would make my app much easier to write, but on the other hand that makes it hard to develop today while Leopard is in beta (at least partly because working day to day in a beta OS is probably not the best idea!) and would cut off a lot of users of Tiger and below. I wasn't this conflicted when Tiger was released, since a lot of its new functionality wasn't as relevant to me; basically, due to the huge volume of changes to how developers work with the system, Leopard *might* be compelling enough to drop support for older systems. It's a difficult decision to make! -- RobRix

----
Leopard is an interesting case, because it is bringing so much which will be useful for developers, but very little which will be immediately desirable for users. So at the same time we may have a greater desire to go Leopard-only than for any previous OS release, while having fewer users upgrade quickly than for any previous OS release.

For my work, we started shipping Tiger-only apps about a year ago, and I anticipate it will take a similar amount of time (around a year and a half) after Leopard's release before we start writing software which will require it. What you should do will obviously depend a lot on your particular market. Quite a few people seem to be planning on Leopard-only releases and they will probably do pretty well with them, while others are still writing Carbon CFM apps to support Mac OS 9. -- MikeAsh

