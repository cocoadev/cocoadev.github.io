---
layout: page
title: RemainingSecondsForDownloading
---



I use a NSURLConnection to download files.

I got stuck on ow to show the remaining seconds for a download, can anybody help me here?

Tnx

----

As data comes in, use the elapsed time and the amount of data recieved to estimate a speed. This can be something simple like     data_size/time_elapsed or more sophisticated algorithms that only pay attention to recent data to take into account varying network conditions. Then simply take the amount of data remaining, divide by this speed, and you will have an estimated remaining time.

