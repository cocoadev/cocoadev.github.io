---
layout: page
title: SimulatingKeyPressesInBackgroundApplications
---



While I am running certain applications, they seem to swallow all the key presses including global hot keys.  What I am trying to do is simulate a key press in a background application so that while I am running one of these applications certain key presses can be routed to these background applications.

(Specifically World of Warcraft in the foreground and Remote Desktop Connection (connected to my XP box) in the background, running Ventrilo)

I can certainly globally post keyboard events, but they go to the frontmost app.  I already have it so that certain key presses are grabbed and a message is piped to RDC.  I just don't know how to simulate the key press.

Incidentally, I'm doing this using APEs and via manipulating the carbon event loop.

-VinayVenkatesh

