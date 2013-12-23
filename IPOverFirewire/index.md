---
layout: page
title: IPOverFirewire
---

http://developer.apple.com/firewire/IP_over_FireWire.html

Apple has a preview release of IP over Firewire. I thought this might be of interest to developers using XCode. I was wondering if anyone is currently using IP over Firewire and building projects in XCode using the distributed builds feature. The main thing I am wondering is what kind of performance can you expect to get from, say, a setup with two single CPU G4s each running at a clock speed of 1 GHz. Is anyone using a setup similar to this on a daily basis? How much help does the second CPU really provide? How many nodes can you connect with Firewire? Basically, any discussion on the subject would be nice. --zootbobbalu

----

When was that posted? IP over firewire is built into Panther and is an option in System Preferences. I think that page is very old.

----

That's great that it is no longer in preview release, so is anyone using IP over Firewire for distibuted builds? I remember reading a report about distributed builds last year, and the main hardware requirement was a fast network connection. Does anyone know if IP over Firewire has a bandwidth anywhere close to 400 Mbps? --zootbobbalu

----

Now where did I put that 25' firewire cord?

Seriously, I have 2 systems connected via IPOverFirewire and one across the house hooked in to the lan with a 25' crossover cable. My primary machine (w/ the crossover)  has, by far, the slower connection there.

Not sure what the average latency and sustained throughput numbers are but it surely beats 10/100 for short distances.

----

I wasn't planning on using IPOverFirewire as an independent network solution. I'm mainly interested in the performance of distributed builds over Firewire. --zootbobbalu

----

Precisely. You **HAVE** to keep the limitations of the medium in mind. FireWire doesn't sustain its high speed over long distances. Just too far for it to go without picking up even the minutest of interference. A single carpet static zap at that speed would wipe out a whole web-sized jpeg in transit. This mainly useful for clustering and an alternative (read 'cheaper' albeit not as 'professional') way of building a SAN (storage area network).

----

My original post is about the desire to hear from anyone who is using IPOverFirewire for distributing their builds over several macs. The nodes will most likely be located in the same room next to each other so cable length is not an issue. I'll probably be using 3 foot cables since I have a bunch sitting around. 

So does anyone use IPOverFirewire to create extra nodes for XCode's distributed builds feature in a real work environment? If so, what kind of performance gain are you seeing. Any input will be great.

--zootbobbalu

----

If you've got the firewire cables already it's worth trying. At worst you waste some time setting it up. At best...?

----


*Cables - check
*"OS X" - check
*One Mac - check


See my problem. The real reason I wanted to ask this is because I am stuck between buying a Dual 1.8GHz G5 and a 1.6GHz G5 iMac. If people say that distributed builds are pretty snappy then I might seriously consider going with the G5 iMac and buy a laptop too (I don't have a laptop at the moment). So, what I'm trying to figure out is if three average macs can perform close to one Dual 1.8GHz G5. --zootbobbalu 

----

So basically, it works out to: Current Mac+Dual 1.8 (vs) Current Mac+iMac 1.6+laptop

Going just off the number and speed of CPUs, the Dual 1.8 is the better addition. If it was me, I'd get the...hmm, I don't have a laptop either...Powerbook, I choose you!

