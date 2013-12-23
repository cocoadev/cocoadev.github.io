---
layout: page
title: UsingPacketsAndSockets
---



Hi, I'm writing my first socketbased app ever and I have some wonderings...

I've written a small server and client that sends packets to each other. I'm using General/AsyncSocket :)
Is there anything special to think about when sending packet of data? Should I implement some sort of encoding (i.e Huffman)?
I have used General/NSDictionarys that I encode as binarys and send away, is this a safe way?

When sending files, is there something extra to think about? I'm reading 2048 bytes from the file each time and just sends it
In the other end I just add the incoming data to my file there... Should I have checks here too?
Whats the "standard way" of doing these kind of things?

Thanks in advance!

----
At some point you will probably want to construct an initial header packet of some sort that contains meta deta about the data to come - eg what kind of data, how many parts, etc.  it will help later on when you go to unpack everything and will confirm at the receiving end that you have indeed received everything.

General/EcumeDesJours
