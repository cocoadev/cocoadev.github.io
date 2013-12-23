---
layout: page
title: ObjCSoap
---

Hello,

I am trying to create an Objective C project to make SOAP requests and receive SOAP responses from a web service.
I created the stubs using the supplied WSDL file with WSMakeStubs and wrote some code, but I get a "in _parseFault" error
when attempting to run my code.  

I followed the guidelines found in the following Mac Dev Center article:

http://www.macdevcenter.com/pub/a/mac/2003/02/04/omnigene.html?page=2

which all seemed to work just fine, except for the parse error, which has me stumped.

Has anyone else had this problem?  Does anyone have some working Objective C SOAP code I could look at?
If this is a fundamental flaw, say in the WSMakeStubs generated stubs (perhaps a type error?) is there a workaround?

I am a new Objective C programmer so I'm still learning and do not yet know how to diagnose the cause of the parseFault or
even where it is ocurring.

Thank you!

ElisabethFreeman

----
Elisabeth,

I think that there is a bug (or two) with the stub files that WSMakeStubs creates or in the way that Cocoa works with Web services. I used WSMakeStubs today to make ObjC stubs for my SOAP server. Hooked them into my ObjC project, and got that same "in _parseFault" error. Creating stubs with WSMakeStubs for AppleScript, or even .Net on my Windows box in VS2002, from the same WSDL file worked just fine. Strangely, some SOAP servers from xmethods.net work with WSMakeStubs and some don't. Maybe someone can shed some light on that. 

As of this writing WSMakeStubs is version 0.2.

-Chris
----

My book BookMacOSXADT has two chapters that show both XML-RPC and SOAP implementations using PHP and Obj-C. They both contain a PHP client and server and an Obj-C client. It sounds like this is what you need. You can visit the book's web page to see the PHP version in action and all of the source code is downloadable as well. SOAP (and any client/server implementation) can be very difficult to debug when you are just starting out so don't get discouraged. Hope this helps. -- JoeZobkiw
----
What is your actual definition of your HelloWorld method in .NET? I did a couple of tests (some services on Xmethods) and they worked just fine. I have VS.NET on one of my boxes here and I could try to reproduce over the weekend.

