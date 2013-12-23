---
layout: page
title: NSThreadAndMDFive
---

Hi.

I'm currently working on a fancy-pants MD5 application. So I thought this was just destined for threads. I thought along the lines of this:
All files larger than 1 GB get their own thread for calculation
All other files get their own thread
File packages (.app, .pkg, etc) get their own thread.

Because I thought doing things simultaneously would speed things up. But as it turns out (after lots and lots of tests) it doesn't speed up anything.
I tried it on my PowerMac G5 2x1.8 GHz and it was slower than non-threaded (meaning, all files, size doesn't matter, are calculated in one thread one after another).
First I thought, well, the bottle neck sure is RAM. So I opened up the ReadAFilePieceByPiece and got some very nice responses and helpful comments. So the RAM issue is, well, worked out, RAM shouldn't be an issue.

So it probably has to do with the processor's speed, right? Most likely, though, it's depending on both RAM and processor speed.

Now I just want some suggestions on what you would do. Would you try another approach and, say, spin off an own thread for files smaller than 2 MB, so these small files are out of the way? Or would you just go non-threaded and live with it? I thought I'd do some good and use the power we have nowadays with our GHz computers, turns out I can't ;)

Thanks for your input,
--MatthiasGansrigler

----
Unless you have a massive RAID and a slow CPU, the bottleneck is disk. On my Mac Pro, openssl can do nearly 400MB/s of MD5 on a single processor. (You can test what your machine does by running     openssl test.) My hard drive will do 50MB/s on a really good day, so pushing the MD5 from 400MB/s up to 1600MB/s on four CPUs won't do anything at all to help.

RAM can certainly also be the bottleneck once your disk read speeds surpass your computation speeds. Your two CPUs share a single set of RAM. Your RAM bandwidth is totally shared, meaning that if both CPUs are saturating the bus then they'll each get half of it. If MD5 is a RAM-limited algorithm (which wouldn't surprise me) then multithreading it won't help at all, since you'll just have two CPUs sitting idle on your RAM twice as much.
----
Performance wise (not regarding the HD, RAM and other bottlenecks) it would theoretically be faster with 8 threads on an 8 core machine, right?
Currently, I support one core machines by running just one thread, and two core machines by running two threads. I could go up as high as four threads (that's all I need for full performance) to make use of the power these multi-core machines give us.
The final question remains: how much simultaneous file-reading can a standard hard drive cope with? I think it can handle two threads alright. But 4 would be... tricky, at least ;)

P.S.: "openssl test" in the terminal didn't work for me - I probably misunderstood you...

Thanks.

----
Try to visualize what would happen if you ran multiple threads like this.
Say you have two threads. Assuming each of your two cores can do md5 at 400MB/s and your HDD can do 50MB/s. You are reading the file in 50MB pieces.
In the first second, 50 megabytes will be placed into RAM.
In the second second, 50 more MBs will be placed into your RAM, and the first piece is processed by the first core.
In the third second, 50 more MBs will be placed into your RAM, and the second piece is processed by the second core.
In the fourth second, 50 more MBs will be placed into your RAM, and the third piece is processed by the first core.

And so on and so forth. So in any given second, only one of the cores will be in use. The only way to force multiple cores to be used would be force reading the file in pieces larger than 400 megabytes, which doesn't sound very good. --ACoolie

----
Sorry, the command is     openssl speed. You can give it a specific suite to test, so to just do the MD5 speed test, do     openssl speed md5.

As far as reading files in huge pieces, that won't help either. You pay for the cost to read the files regardless, and you'll never be able to go faster than that.

