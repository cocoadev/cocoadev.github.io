---
layout: page
title: DetermineIPandSerial
---

I'm trying to make an app which can fetch the machine's IP Address, Fully Qualified Domain Name (FQDN), and Serial Number.
I've managed to make the interface, but I'm lost with the code...
I have no idea where to go from there.
I'm new to mac programming, and I'm not having much success with the documentation.

Can anyone help point me in the right direction?

----
Why do you need the user's computer's serial number? If you tell us what your ultimate goal is, we may be able to suggest a simpler alternative to collecting all that info. -JonathanGrynspan

----

Also, a small warning, in case you hadn't considered it: Laptop users. Their fully qualified domain name may actually change, depending on which network they're connected to. Consider a home user connecting to a directory server at their place of employment. Or someone connecting to many different networks, as their occupation demands. If you're using it for, say, registration validation or any kind of persistent identification, you may be unpleasantly surprised at the FQDN's unreliability as a "unique identifier". The FQDN, in this case, is more useful for "profiles" of the various network states in which the computer can exist.

Beyond that, look into "gestalt" (search this site and the cocoa-dev mailing list archives, and google the term in general) to get a wealth of system information. I believe the machine's serial number is available there. Check into     NSHost (the     +currentHost class method) to get host information on the machine on which your app is running (the 'current host'). Check out all of     NSHost's instance methods -     -name and     -address, particularly. This should get all the information you asked for.


----
We gather this information to support users on our network. I have an app I made for our windows machines which gathers this exact info, and I'd like a version for our macs. FQDN is required to determine which machines are and aren't supported.
----

After some searching, I found some apple sample code for getting the serial number.  
See technote 1103:
http://developer.apple.com/technotes/tn/tn1103.html

