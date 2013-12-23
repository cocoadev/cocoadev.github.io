---
layout: page
title: AsyncPOpen
---

I have something like this in my application:
    
if(FILE* fp = popen(..., "r"))
{
   while(fgets(fp, buf, sizeof(buf))
      ...
   rc = pclose(fp);
}


The problem is that it is blocking my application, and there is no way that the user can abort the command being executed.

So what are the alternatives? I know of NSTask, but this is far to cumbersome, because I have some command strings, but NSTask wants a command and an array of arguments, which means I need to parse my strings into command arrays (and de-escape escaped characters, as NSTask seems to do misc. stuff when composing the command string) -- furthermore, it seems that NSTask can send an NSTaskDidTerminate notify *before* the final has-data-available notification.

I can place the code above in a method and use NSThread to execute that method in its own thread -- but then how to stop the thread? it seems that only the thread itself can stop (since exit is a class method which forwards it to the current thread).

I can get the file descriptor of the file pointer returned by popen() and either wrap it into an NSFileHandle and read in the background, but will this send a notify when the process exits? or I can set the descriptor to be non-blocking and simply poll it every second and also check a semaphore which the main task can trigger if I need to quit the thread, but polling is never the answer.

Alternative I can set the descriptor to be async and setup a signal handler for SIGIO, but this handler doesn't get any context info, so I need to resort to global variables, which is also bad (especially as I need to spawn several threads)...

I have also looked into distributed objects, but somehow it doesn't really solve the popen()-problem...

What am I missing? is it really that hard to execute an interruptable task in the background, if one doesn't want to use NSTask?
----
It has been my experience that using NSFileHandle asynchronously with the NSFileHandleReadCompletionNotification notifications will send you one last notification with a Zero (0) length NSFileHandleNotificationDataItem property. Meaning the NSData will have a length of 0. At this point it would be safe to clean-up. I have never seen this mechanism send a notification with a zero length NSData unless it had found EOF or an error (indicated with the NSFileHandleError property). I can't remember where, but I seem to remember seeing this documented somewhere.

----

You could try looking at ThreadWorker and adapting that code to make the cancel operation abort the file read.

If you don't care if you keep reading the data, though, you could just use the async behavior in NSFileHandle and then discard the data when the read is finished.

You may also want to try using something like NSData's initWithContentsOfFile: since I think it just memory maps the file (this, of course, means that the file can't be too big).  This way it will be loaded lazily so the initial read will take no time and aborting simply means releasing the object.  Depending on what you are trying to do, this may work best.

--JeffDisher

----

    popen() parses its arguments by simply passing the string you give it to the shell. If you want to achieve a similar effect with NSTask, just have the task run     /bin/sh with     -c as the first argument, and your command string as the second.

--MikeAsh

