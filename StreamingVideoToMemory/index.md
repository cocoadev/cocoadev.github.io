---
layout: page
title: StreamingVideoToMemory
---



I am working on a project in which i would like to take video that is streamed over the internet via multicast and be able to save the information to either memory or to a temporary file on disk.  I have looked into General/NSStream's getStreamsToHost:port:inputStream:outputStream, but when i create a host using hostWithName: it will open up a connection but will never have any bytes avaiable for reading, and when I use hostWIthAddress i get a posix error saying "Address family not supported by protocol family"

Should i be using some other class in order to do this?  Or perhaps am i setting up the General/NSHost variable incorrectly?

----

How about a little more information? What kind of stream are you trying to get (specifically, or are you trying to just get any kind of video stream)? What code are you using? You cannot seriously expect help with vague descriptions. Your guess at the problem is *better* than mine at this point because I am not armed with any of the information necessary to diagnose the problem.

----
Basically I am just trying to get a basic quicktime movie stream from a server that resides on my network.  It is a .mov quicktime stream.  Currently my code to create the stream looks like this.

     
General/NSHost *host = General/[NSHost hostWithName:@"server.name.here.com"];
General/[NSStream getStreamsToHost:host port:5434 inputStream:&inStream outputStream:nil];
[inStream setDelegate:self];
[inStream scheduleInRunLoop:General/[NSRunLoop currentRunLoop] forMode:General/NSDefaultRunLoopMode];
[inStream open];


where inStream is an General/NSInputStream variable.  
also in the stream:handleEvent: function the eventcode will either return General/NSStreamEventOpenCompleted and then never call again with General/NSStreamEventHasBytesAvailable (thats when using hostWithName)
or it will return General/NSStreamEventErrorOccurred when using hostWithAddress (this is where i get the error code from)


I hope that is enough information.  If you need more just ask.  Sorry about being vague before... wasn't quite sure everything you would want.

----

This method creates a TCP connection to the server on the specified port, which is completely different from multicast. What are you really trying to do?

----
I'm basically trying to capture the video from the multicast stream, and save it to memory or to the disk, to access it later.

----

Yes, but when you say "multicast", do you actually mean IP multicast as described in RFC1112 ( http://www.faqs.org/rfcs/rfc1112.html )? If so, you're going to have to write a fair amount of custom code, or find a library that takes care of it, because Cocoa doesn't have anything multicast-aware. If not, then we need to know what you're actually trying to do before we can help you.

----
Sorry i misunderstood... I do mean IP Multicast.  Would the omnigroups omninetworking be a good library to look at.  I saw that it was able to handle multicast sockets.
All i really am doing is saving a quicktime video from a ip multicast stream to either memory or to the hard drive (depending on the size of the stream), so a person would be able to watch it later when they are not connected to the internet or the stream is not currently playing what they had wanted to watch before.  It would end up being kind of like a PVR.

----

This may be an incredibly stupid question, but doesn't the General/QuickTime framework allow you to save streams already? If so, you're reinventing the wheel. If not, then I'll kindly shut up. ;-)

----
You are able to save streams in Quicktime... but only the address of the stream, so when you try to use that file it will just reconnect to the stream.  What i want it to do is actually save a copy of the stream's data that would be accessible at any time, whether the internet was available or not.

*Would this help?* http://sourceforge.net/projects/streamripperx/

That seems like it is for tcp radio streams, where i need to be able to get save both video and audio from an IP multicast stream.  But thanks for the help.

*Well, my point was that it's clearly not something that's easy to do or ready-made. This might be able to be adapted to your purposes.*
