---
layout: page
title: NSPipeAndfcntl
---

I wish to launch a command and show the result of this command in the GUI as it becomes available.

For this I was using NSTask with an NSPipe for its output, which I read from in the background.

Unfortunately data only becomes available when the underlying buffer is full, which is everything but desired in this situation.

I have tried to disable buffering using:
    
NSPipe *outPipe = [NSPipe pipe];
int fd = outPipe fileHandleForReading] fileDescriptor];
int err = fcntl(fd, F_NOCACHE, true);


But I get -1 back from fcntl() (which indicate error).

Anyone found a solution to this problem?

I realise that caching might refer to file system caches (i.e. used when re-reading a file), but then what controls buffering? I am aware of setvbuf(), but this would be for the C layer, and as such should not interfere with file descriptor buffering.

For the records, I have also tried to periodically fsync() the file descriptor, but without any luck.

----

If line-by-line is good enough then you may want to run it under a pty pseudo-terminal.

The method that I use is to use the pty program as outlined in "Advanced Programming in the UNIX Environment" by Richard Stevens. It can be built from the sources at http://www.yendor.com/programming/unix/apue/ch19.html which will also require the sources at http://www.yendor.com/programming/unix/apue/lib.44/

-[[NeilVanNote

