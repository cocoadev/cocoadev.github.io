---
layout: page
title: NSProtocolCheckerConcerns
---



Assuming you're vending an NSProtocolChecker for a protocol that doesn't have the     -privateMethod method included, and it's stored in the     remoteObject variable on the client side, is there anything that prevents the client program from evading the NSProtocolChecker simply by saying:

    remoteObject target] privateMethod];

instead of:

    [remoteObject privateMethod];

As far as I can tell, in the first case the     -[[[NSProtocolChecker target] method never reaches     -[NSProtocolChecker forwardInvocation:] and so is never subjected to the protocol check. Then the DistributedObjects system creates a proxy for the real object, bypassing the NSProtocolChecker.

Am I overlooking something? Is there some magic that takes place here?

----

I suggest simply trying it and seeing what it does. I'd guess that there's something in place that prevents this bypass from working, but I don't know what it would be.

*I'll definitely do that before I start relying on it.*

----

Check out -[NSObject replacementObjectForPortCoder:].  NSProtocolChecker may or may not be smart about its target method, but you can definitely implement replacementObjectForPortCoder: on the target class to avoid the problem.

*That's right, I forgot about that... so this won't be nearly the problem it'd be otherwise. Thanks!*

