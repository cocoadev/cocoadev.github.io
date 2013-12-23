---
layout: page
title: XgridCompatibleCode
---



I recently heard (and saw the Apple web site) about Xgrid.  There have been a lot of rumors circling around Xgrid, and now that it's finally out, I'm wondering how I can write an app that makes use of Xgrid and automatically distributes itself over my cluster.  Basically, what I'm asking about is some kind of API documentation.  I wasn't able to find any.  How do I instruct my program to spawn processes on other available machines?  Thanks.

----

Well, it all depends on the type of numerics your are trying to do. Are your calculations implicit or explicit? This is the basic barrier you are going to face when dealing with clusters. --zootbobbalu

----

I'm very familiar with parallel processing, both with shared and distributed memory configurations, as well as high and low latency environments... I'm on the verge of writing parallel code and was going to use Rendezvous and Distributed Objects... however, if my life could be made even simpler by Xgrid then I'd be really happy... how do I find out more about Xgrid, and where do I find appropriate documentation?  If anyone knows, I'd be much obliged.

----

The Xgrid preview download has developer information on the disk image.

It is available (6.3 MB dmg.bin via ftp) from:  http://developer.apple.com/hardware/ve/acgresearch.html

----

I put together some information I gathered about the API after trying to develop a custom plug-in. I posted it on this wiki at General/XGridDummyPlugin.
