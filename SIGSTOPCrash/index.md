---
layout: page
title: SIGSTOPCrash
---



I'm not asking "what's wrong with my code" here I'm just wondering what a SIGSTOP is and what conditions might cause one.  Knowing that, I'll try to figure out why I'm getting one.

Thanks.

----

See the man page for signal. You get the stop signal when another process tells your process to stop.

    
     16    SIGURG       discard signal       urgent condition present on
                                             socket
     17    SIGSTOP      stop process         stop (cannot be caught or
                                             ignored)
     18    SIGTSTP      stop process         stop signal generated from
                                             keyboard
     19    SIGCONT      discard signal       continue after stop



----
It should be noted that SIGSTOP should never crash your program, only pause it. Are you sure your app is actually crashing because of the SIGSTOP?
----
You are correct - it's simply hung up.  It enters the debugger.  I cannot continue the app, though, as it quits as soon as I attempt to resume from within the debugger.

