---
layout: page
title: RendezvousResolveProblems
---



Hi All,

I am needing some of your wise advice on a Rendezvous problem I am having:

I have setup Rendezvous in my app and that works fine, and when a new NSNetService is resolved via Rendezvous, I have my app go ahead and request over some basic netwroking code the User image of the remote machine from addressbook so we can give a face to this new netservice.

This all works fine.

Except I have a checkbox that allows the user to start and stop the Rendezvous service. Starting then stopping very quickly isn't a problem, but if you do a start-stop-start to rendezvous, the second time a service resolves my app blows up because (and I'm guessing here) I'm trying to connect to a socket from a netservice that doesn't exist any more.

I was under the impression however that this problem was fixed in 10.3.2 - or so says another page on this site: "In Panther only, if you immediately resolve an address after the NSNetService has been found, then it will resolve it to the address that the service was last used under. However, if you resolve a service about a second after that, then it will resolve under the correct address."

If I *slowly* turn my rendezvous on and off, then it is fine. It's when doing it quickly that my app just dies. I get a signal 13 (SIGPIPE) error in the log.

Is the solution just to have rendezvous on all the time? No user preference? Or should it be buried in a preference pane that disappears when you "Ok" your changes - a la iTunes (which is most un-OS X like by the way - contrast this to how System Preferences mostly makes changes realtime as you change things).

Please help!

Also, I am at the stage of having scraped together some BSD sockets networking code to work - but I'm sure it's a complete shambles, and we all know there are so many things that can go wrong with networking. Yet I haven't really accounted for any of this, I have no time-outs, or handling unexpected data etc. Is there a book that will guide me through countering these pitfalls? Or am I on my own? I mean, I don't even know where to start with time-outs - is there an inbuilt socket 'thing' that will timeout a read or write operation? Is there something buried in NSFileHandler I haven't seen? Any other Cocoa classes I could be using that would make my life easier? Or do I pop things into a different thread and run an NSTimer for timeouts? You see - I have no idea to write networking code.

Any help on these two issues (along the lines of "what I would do" kind of stuff) is incredibly gratefully received!

- nex

many of the high-level socket classes contain methods that include timeouts.  Try AsyncSocket, for instance...

EcumeDesJours

