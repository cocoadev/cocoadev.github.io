---
layout: page
title: ListeningForPhoneCallsThroughModem
---

I am trying to do something rather odd. In my application i am trying to listen for incoming phone calls as to run a method. I honestly have no idea where to begin on this there isn't really much documentation on modem anything on apples developer site, i would imagine it requires use of the BSD sockets. Does anybody have any idea where to begin on this?

Peter

----
It has nothing to do with BSD sockets, which are used to communicate over the internet. You want to talk to a serial device, check out AMSerial.
----
Serial port communication, modem commands, AT Commands are the key to go..
----
Thank you that works perfectly

Peter

