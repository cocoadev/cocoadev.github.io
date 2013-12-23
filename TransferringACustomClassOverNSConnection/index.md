---
layout: page
title: TransferringACustomClassOverNSConnection
---



Is it possible to transfer a custom class, a subclass of General/NSObject over an General/NSConnection?

Right now when I try to hand it over to the remote object, the client crashes after trying to read it (ie. via an General/NSLog()). I  believe it may have to do with General/NSCopying not being implemented? Any ideas?

----

what does your General/NSLog() look like? does it crash without it?

----

    
General/NSLog(@"Object: %@", [remoteConnection sendMeMyObject]);


It does not crash without it. With it, the output is sent, I see an Array of the objects I want transfered over and all looks good, but next comes the crash :)

----

This array... how does it get created? You need to terminate an     arrayWithObjects: list with a nil, for example. What happens if you just call     remoteConnection sendMeMyObject] without trying to log it?


----

My array is fine ;-) The problem is that it contains an General/NSImage and General/NSConnection doesn't know how to get it across I think.

---- 

If you don't show any real code, no one can help you..

General/NSConnection works by proxying an object, not by literally copying it.

----

from my experience with Distributed Object, the usage of General/NSLog() on remote objects is not recommended... be sure to implement the "description" method of your class, but even with this method, i've seen many of my program crashing when using General/NSLog and DO... i've bannished General/NSLog now. Some of those programs works 24/24 since many years with very few restart :)

----

Just got done implementing something along these lines, and thought I would post for posterity... all info needed was found in Apple docs.

To send an object "across the wire" in DO, your object needs to implement the General/NSCoding protocol.  As an additional note (that I didn't encounter in Apple docs), the object that does the coding in DO (General/NSPortCoder) does not seem to support keyed coding, so be sure your encodeWithCoder: and initWithCoder methods are old school, and not keyed.

Your object needs to also implement the following method, in order to tell the General/NSPortCoder to package up and send your actual object contents, not just a reference to the object:
    

- (id)replacementObjectForPortCoder:(General/NSPortCoder *)encoder {
    if ([encoder isByref]){
        return General/[NSDistantObject proxyWithLocal:self connection:[encoder connection]];
    } else {
        return self;
    }
}



Finally, make sure the appropriate method signature specifies the "bycopy" keyword.  The General/NSPortCoder checks this to see what to send.

HTH! - General/JohnPannell
