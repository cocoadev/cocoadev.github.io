---
layout: page
title: InternetChessClient
---

Hi Guys,

I am trying to write an internet chess client using Cocoa. I am an experienced programmer from the Smalltalk world (and hence I love Cocoa) but am unfamiliar with the exact protocol needed to communicate with one of these chess sites (freechess.org).If I open Terminal, and type "ftp freechess.org 5000", it finds the site and gives me a login prompt. So I guess I need to write something that will communicate simple text over tcp/ip but I am not totally sure. There is a program already written that does this called Chessic but it will only connect to a different site, I want to write on for the above site. Any advise, suggestions, etc. would really be apprciated. I looked through the Cocoa API and didn't see anything that jumped out at me. Thanks, Scott McLaughin

----

Are you looking for the FTP protocol, or is this a special protocol for chess?  "Simple text over tcp/ip" is meaningless; there has to be some meaning to the payload of the TCP packets or else you're just spouting garbage back and forth.  If you're trying to use FTP, CFNetwork can do that.  But what you really need to do is BreakItDown and learn a bit more about how the Internet works.

----
Thanks a lot for responding. True, I don't really know how this all works. The chess server sends/receives information back and forth as simple text with information in 28 different slots. I have written a parser for this info but I don't know how to connect and send/receive information back and forth. It must be some standard kind of connection that is not hard to write but I don't really know what is needed. All clients connect via port 5000, I know that much.

----
It's most definitely a custom protocol.  You should learn how networking works in general, then look into how you do networking on the Mac (CFNetwork and/or the Cocoa stream classes).  You have this big concept right now ("I want to write a networked chess program for the Mac"); what you need to do at this point is research each of those three things: "networked" (means learning how networking works, both in the theoretical and practical senses), "chess program" (what does my program need to do in order to be useful?  This is known as the *scope* of a project, and is a lot harder to nail than it sounds at first), and "for the Mac" (how do I write applications for the Mac?).  Once you've done a lot of research, then you can sit down and design your application top-to-bottom.  Only after you've sufficiently designed the components of your application, starting with the highest levels and working down to the bowels, can you start coding from the bowels back up to the top level.  Tried-and-true software design methodology.

----
In other words, BreakItDown.

----
I am also working on a FICS client. I was able to use AsyncSocket to connect without any problems. (FICS uses telnet.)

