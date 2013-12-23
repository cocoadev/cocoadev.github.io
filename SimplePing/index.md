---
layout: page
title: SimplePing
---

If you're looking for a very quick and simple way to add synchronous ICMP ping support to your app, here's how:


* Grab Apple's SimplePing sample code from:

http://developer.apple.com/samplecode/SimplePing/SimplePing.html

* Open SimplePing.c and add the following lines to the top of the file, after the includes:

    
#define printf
#define fflush


This stops the code from dumping all those printf messages to the console. Now we need to change the code to return the number of ping packets we successfully received:

* Change the declaration of the SimplePing function to the following in the .h and the .c file:

    
int SimplePing(const char* HostToPing, const int NumberOfPacketsToSend, const int PingTimeoutInSeconds, const int ReturnimmediatelyAfterReply,  int *numberPacketsReceived );


* Delete the local declaration of "int numberPacketsReceived" in the SimplePing function. We've added "numberPacketsReceived" as a param so we don't need the local declaration.

* Change the usage of "numberPacketsReceived" in the SimplePing function to "*numberPacketsReceived". 



That's it. It's a bit of a hack, but if you're in a hurry it'll do the trick.

