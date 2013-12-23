---
layout: page
title: HowToAvoidSendToAppleWindow
---

Hello, I am writing an application, that needs to crash :)
But every time it crashes, it suggest to send crash log to Apple :)
How can I avoid this?

----

App that needs to crash, eh?  Well, whatever floats your boat. :-)

If you mean that you'd like to stop all the annoying crash dialogs while you're developing, see http://developer.apple.com/qa/qa2001/qa1288.html .  That gives instructions for globally disabling the bug report dialog.

If you mean you'd like to avoid the dialog for users as well, take a look at the Adium source code.  They have a scheme to grab bug reports for themselves and skip the Apple dialog.  I believe it uses a child process that really runs the app and a master process that catches crashes in the child.

-KenFerry

----

Why does it *need* to crash? What kind of crash does it need to do? You might be able to get away with registering a signal handler for whatever signal gets sent when you crash, and having it call     exit().

----

Okay, so you're writing a Windows app? ;-)

----

yeah yeah, that's the ticket - it's crashing because it *needs* to crash

----

Duuuuuuuude. That's *deep*.

