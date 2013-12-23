---
layout: page
title: DistributedObjectMeasurePerformance
---

Hey

Everyone knows that distributed objects suffer a performance hit when compared with just using typical sockets. Has anyone measured what this performance hit is?
I am specifically interested in the performance hit when used with sockets to do distributed computing. I am looking for hard numbers. I am currently writing up two mock clients and servers. One using DO, the other plain BSD sockets. I was going to send a synchronous messages and a asynchronous on both and then measure the difference. I am not sure how much information that will give me on the performance impact. (I am of course going to try sending multiple messages and messages of different sizes etc). I am mainly curious if anyone else has done/attempted this and what there result maybe. I looked on Apple for some statistic, but unfortunately found none. If someone else has been fortunate enough to find them could you point the direction?

Thanks

----
As far as I know, there are none. However, it's a complex question and I'm not sure how useful a general benchmark would be. If you're going to do them yourself, consider the following items:


* Latency: how long does it take to get a response for a single short message?
* Bandwidth: how much can you squeeze through the pipe when sending large amounts of data in bulk? Another way to put this is, how much encoding overhead does DO have?
* Ability to queue requests: you can send multiple requests over DO using oneway void messages, but then it's a pain to get the responses back. This helps eliminate some of the latency problems.
* Encoding overhead: if you're sending complex objects over the wire, how much CPU time is spent encoding them with NSPortConder versus how much you spend encoding them with whatever custom code you would use?


Overall I would consider DO to be a poor choice for distributed computing unless it was an embarrassingly parallel problem that doesn't require much communication, such as Folding@Home and SETI@Home do. However, I have not tested it and it would be interesting to see numbers.


----

Hey

I have taken almost all the points into consideration (it only made me cringe and hence my initial post hoping someone else had done the dirty dirty work). As for the DO being a poor choice, I fully agree. I am more interested in the numbers for a report. I will post number and how I obtained them when I think of the best way of doing so.

Thanks

