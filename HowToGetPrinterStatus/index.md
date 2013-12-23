---
layout: page
title: HowToGetPrinterStatus
---

I make a code to send poscript files to printer.
It's work fine but when the printer is not ready, the user send and send file to it and request stay into printer queue.

When the printer will be ready all files (same file) will printing.

So I want to know if it's possible to get the printer status before send file to it ?-

----
`lpstat -a` will give you the status of all the printer queues, so there's gotta be some way to ask CUPS.  Google for the CUPS API, or try `apropos cups`.

----
Ok I try to get printer status with the CUPS API.
I can just request the CUPS server but not directly the printer !!!

When I shutdown my printer CUPS don't know this ...
So, my question is simple: can develper know the really printer status (not CUPS status) ?

----

If your printer is on the network, have you thought about using ping? Also, don't mean to be rude but this isn't really a cocoa-related question is it? A forum on the CUPS software might be a better place to ask your question.

----

Cocoa is a collection of frameworks to build application ? isn't it ? and CUPS is the system validated by Apple to control printing, isn't it. So how developer can works with this if he can't do what he wants ?

----
Yes, Cocoa is a collection of frameworks but that does not mean that it encompasses all such frameworks. CUPS is not included in Cocoa, it is separate.

There is a printing mailing list on http://lists.apple.com/, maybe you should try there. In any case, rest assured that this site is not the right forum.

----
Ok, I know now that this site is not the right forum to build a clever application.

----

You're right - we only build stupid dumb applications here.

----
While you're at it, why don't you try asking some Cocoa questions on the printing mailing list. No doubt you'll find out that's not the right forum to build a clever application either.

Surprise! There are different places to talk about different things. This is CocoaDev, not AnyRandomMacAPIDev. When you wander outside the bounds of Cocoa, you need to find a better place to talk about it.

----
this problem is old :
http://lists.apple.com/archives/printing/2004/Mar/msg00101.html

I hope the futur Leopard system will work fine with printing system and will give to the developers many new features...

JM Marino - http://jm.marino.free.fr

----

Sounds like the perfect opportunity to log a bug!

