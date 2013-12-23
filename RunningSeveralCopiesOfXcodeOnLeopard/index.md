---
layout: page
title: RunningSeveralCopiesOfXcodeOnLeopard
---

On WWDC '06 they said we'd be able to run Xcode 2.4.1 and Xcode 3.0 simultaneously on Leopard to be able to write software for Mac OS X 10.4.x

However, I just tried installing Xcode 2.4.1 and it just told me that no copy of mac os x 10.4 was found so it couldn't install...

Any ideas?
--MatthiasGansrigler

----

Get Xcode 2.5.

----

Another solution would be using SDKs -- you can program for 10.4 (and 10.3.9) even within Xcode 3. -- EmanueleVulcano aka millenomi

----
A very good point. The only reason you need Xcode 2 is if you're going to be sharing projects with people on 10.4. If you're just writing apps for 10.4 then Xcode 3 will do fine.

----

In fact you don't even need Xcode 2 in the latter case -- as the project format is identical and you get warned (in the project's info pane) if you are accidentally using any feature that is Xcode 2-incompatible.

(Yep, it really seems Apple learned from the PB -> Xcode 1 -> Xcode 2 debacle.) -- EmanueleVulcano aka millenomi

----
Well, I don't think Xcode 3 for 10.4 works, just try making a simple button in IB. It'll tell you that some image scaling thing is not available in 10.4... Or did I miss something?
An empty Xcode 3 project I compiled didn't run on Tiger...

----

Xcode 3.0 will not work on any version prior to 10.5.

----

I obviously expressed myself wrong. I mean, I know Xcode 3 itself doesn't work on 10.4, obviously. What I meant to say is that compiling empty projects in xcode 3 linked against the 10.4 sdk don't work on tiger...

----

Did you remember to set     -mmacosx-version-min=10.4?

----

Adium compiled on Leopard with Xcode 3 definitely works on 10.4, so it is doable :)

