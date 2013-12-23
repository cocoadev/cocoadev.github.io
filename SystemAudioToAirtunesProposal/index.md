---
layout: page
title: SystemAudioToAirtunesProposal
---



I am beginning to learn cocoa programming to develop an application that will stream any system wide sound directly to an Airport Express base station. The idea I had in mind was a system menu bar that would automatically detect any Airtunes networks (Living Room, Bedroom, etc) and allow you to easily stream the audio output of your powerbook to the base station. Is anyone aware of any projects already underway to do this? If not, does anybody believe this a feasible open source project?

----

Sounds like a great idea, but beware: should it get too popular, there's a good change Apple will *intentionally* break it. As much as we all love Apple, they can be a might bitchy when people play with their private toys.

Also, keep in mind there will probably be some heavy latency -- General/AirTunes is almost certainly optimized for music streaming which is, by nature, something that can be read-ahead cached. System sounds don't work so well this way.

If you want to figure out how to do this, you'll need to figure out how to capture system wide sound (could be trivial, or maybe not. I don't have any idea), and you'll probably want to scope out the reverse engineering work done by DVD-Jon, as recently plastered all over the web. You can probably find a lib which will do the broadcasting part.

--General/ShamylZakariya

----

I'd like to join this - could you please contact me? --General/MartinHaecker
