---
layout: page
title: NetSocketPerformance
---

I am building an application to send binaries around to specific locations on a LAN and was using the NetSocket class.  All well with smallish files (<20megs) and I was getting about 80% of the performance of copying the equivalent file with AFP.  With large files (>100megs) performance is really not very good on a gigabit network, something like 10% or less performance ratio compated with AFP (ie mounting the remote drive on your desktop and dragging the file over).  What could this mean?  perhaps I need to scale the packet size in some way to send larger packets?  right now I am just writing the entire file to the netsocket and getting it via a filehandle on the other end.  The initial packet I send contains metadata about what the file is, how large it is, etc and I am filtering that out by looking for a packet corresponding to my metadata size and then writing anything else to the filehandle.  Everything works fine - it is just slow and bogs down the system considerably with large files.  Anyone tried to do something like this before with one of the higher level CFSocket networking wrappers?

EcumeDesJours

----

I've written my own class, AsyncSocket, so I never used NetSocket, but looking at the source code, it might be flooding the kernel's send queue and is definitely copying around large chunks of data.

Maybe you should try AsyncSocket for this. It does less (or no) copying, is more intelligent about queuing write data, and is more packet-oriented than NetSocket. If you do choose to try it, let me know how it goes. Unfortunately, I have not tried to send large amounts of data with it, myself.

�DustinVoss

----

Hey Dustin-

I looked at AsyncSocket but it was hard to beat the documentation that NetSocket came with (beautifully documented API).  Now that I am having some issues I may reevaluate that decision.  Another thing that is weird/unfortunate is that I assumed that there would be some sort of send/receive temporal parity but NetSocket writes everything to the socket as quickly as possible and then returns the allDataWritten message while it takes far longer for the other side to receive the data so I had to write in code to have the other side send update packets back to the sender documenting its progress, along with an EOF at the end so the sender would know that everything had arrived.  Maybe it is normal to have to do this (this is my first network app) but I am sure it is also adding to the inefficiency.  

EcumeDesJours

