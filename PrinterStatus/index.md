---
layout: page
title: PrinterStatus
---

Hi,

I want to know if it's possible to get the default printer status. Perhaps with CUPS ? Is there a class that implements this?

-- JMM CodePark ( http://jm.marino.free.fr )

----

Depending on what you mean by 'status' NSPrinter might have what you want.

----

Ok, I want to know if  :

* the printer queue is empty,
* there is a printer error,


So I want to get all of status printer.

----

see the man pages for lpstat and lpq

----

I see the man page for lpstat and I try this for the default printer status :

lpstat -p `lpstat -d | awk -F': ' '{ print $2 }'`

printer _125.0.2.20 is idle.  enabled since Jan 01 00:00

OK but the printer is out of paper and the function doesn't know this status ????
BAD BAD BAD ...

----

Try the PrintCore framework, part of the ApplicationServices UmbrellaFramework

----

nothing for me in this framework... I think it's IMPOSSIBLE to know the status of printer, I back to LINUX !!!  **This statement is ridiculous.**

*why do you care? the printer driver knows whether the printer is out of paper, and will notify the user if it is. enjoy linux.*

----

In my app I need to know if printer is out of paper because I should disable print item menu.

*
That's unwise. 
* I may be well aware I'm out of paper and want to make a pdf before they go out to the store to buy more.
* I may want to fax instead of print
* What if I have 2 or 3 printers, but only one is out of paper, and it's the default printer?

*

----
There are plenty of reasons that an app might want or need to restrict printing to a particular printer, and why it might disable printing if that printer isn't ready to print. The "don't do that" advice makes sense if the OP is writing, say, a word processor or other general purpose app. But if he or she is writing an application that prints checks, postage, concert tickets, stock certificates, or anything other sort of official document, there may well be a legitimate need to ensure that a document prints exactly once, on a certain kind of paper, at a certain time, etc. -CS
----
----

Why it's not possible on Macintosh ?

*it shouldn't be possible. you shouldn't have to ever care about this at an application level.*


----

How to do so ?

---- 

The point is that the print menu should *not* be disabled in this case.  Allow the OS to handle it.

---- 

What do you think about cupsGetJobs() function to see if printer is ready or not ?

JMM
http://jm.marino.free.fr
----
I'll second those above saying that you shouldn't do this! Sometimes I even print with my printer disconnected, I quit the app, and when I find the time I connect the printer, and out comes what I printed -- this is a nice feature!

----
I reply to my self

Just use SNMP protocol to get printer status...

JM Marino
http://jm.marino.free.fr

