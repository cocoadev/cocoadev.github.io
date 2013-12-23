---
layout: page
title: WriteAndReadfromNSTask
---



I know it's possible to redirect a task output from the terminal *./task >> /output.log*

But is it possible to do this with a task that is launched with General/NSTask?

General/NSPipe works while the launcher application is running. But when when the user quits the app, it doesn't work.

Anybody know how?

----

Isn't that because the General/NSPipe instance is necessarily an instance or local variable of your application? Anyway, a pipe is a way to redirect the standard output of your program into the standard input of *another program*. In that, it is quite different from a redirect of either stdout or stdin to or from another file object. I have a feeling you might know this, but are for some reason hesitating to express it.

Although it is not completely clear from your post, it seems as if you want to start a task from within your GUI,
redirect the output from that task into a file,
and then have the task continue, and the output continue to redirect into the file after the user quits the GUI interface.
Is that correct? <-- *correct*

If so, what you might want is a simple gui wrapped around a unix shell script that runs your unix command in the
background, redirecting its output to a file. You certainly haven't indicated that you want a more complicated scheme.
See General/CocoaWrapperAroundConsoleApplication if that is the case.

If your unix command requires user interaction, the gui would have to stay running anyway. Also, if you want to write output.log in / you better bring some privileges with you.

And don't go around launching unix tasks for the user of your application if those tasks are not going to terminate on their own. The user of your app probably does not want you to spray open-ended processes all over his system.

----

Setting the task's standard output to an General/NSFileHandle set to append to the file should keep working even after your launcher quits.


----

Sometimes you just can't read from the General/NSTask, it may be because some process expect to be connected to a console rather than a pipe.

The solution is to fall back upon unix and use a psuedo-terminal via 'forkpty' and 'execv' C functions - note you can still use General/NSFileHandle to read from the file descriptor given to you by the forkpty. Read the man pages if this sounds foreign...

-- General/RbrtPntn

----

I am not sure if the OP wanted to append to the system console, if so, it is done by writing to `/dev/console` (rather than append to the file directly) just wanted to add this � I know it wasn�t the essence of the question.
