---
layout: page
title: IRCPong
---

Hi all,
      This is Syphor here again :) Can anyone explain to me how the IRC PONG system works?? I know when ther server PINGs the client, the client is suppose to reply with a PONG. I have made a small basic cocoa app that connects to the specified irc server using small sockets and displays it in a NSTextView, but I can't figure out how to PONG the server... because after 30seconds it disconnects me from a ping timeout.

I tried something like this

    
[socket writeString:@"PONG :irc.dal.net"];


Though it doesn't work :(

Has anyone had any luck with this?

----

Forgive my rudeness, but perhaps these questions should be directed someplace else? This site is specifically for Cocoa programming questions, not specific questions such as this...

And I don't know the answer to your question, but good luck. Try the RFC's for IRC. IIRC I think the number is 1492, but you'll have to double-check.

----

This is cocoa programming.. I'm making a Cocoa IRC Client (Obj C)

