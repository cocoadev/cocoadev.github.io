---
layout: page
title: HowToWriteAnAppThatKillsAnotherAppBasedOnInactivity
---




I need to write an app that kills another app based on user inactivity.  More specifically, some short period before the OS X "screen sleep" event takes place (based on a user being inactive for a set amount of time) I need the app Hardware Monitor to die.  The reason for this is that while Hardware Monitor is communicating with an external LCD display (4-line status LCD), OS X cannot sleep as the drivers present the LCD readout as a human interface device, and if it's receiving a datastream, it's as if the mouse is being moved or some other HID event.

More on that external LCD here:
http://www.bytecellar.com/archives/000081.php


I'd like an app that polls the system, say, every 10-15 secs to see how high the "user idle counter" or whatever it must be called in the system, has gotten.  If I've got screen sleep set for 15 mins, I'd like to trigger the death of the Hardware Monitor app at, say, 14 minutes.  

Can this be done?  Simply?  Thanks.

NOTE: The Hardware Monitor app author tells me it's impossible to implement something like this into his app because a user app cannot snoop such OS-level data.  Others tell me it can be done.  Adium and iChat report how many mins user has been idle.  Same kind of thing, right?

----

Use HIDIdleTime

----

http://macenterprise.org/content/view/121/140/

...not far from, I suppose.

----

I could just write a cronjob one-liner that ran every minute that killed the PID for Hardware Monitor if the returned # of secs is a certain value, right? 

Anyone know the syntax for that?  Tnx.

----

Well I don't know about the oneliner because ps is braindead when reporting the process startime.

This prints the seconds since a given process has been started but using a C tool that computes the time difference.

cristi:~ diciu$ cat timediff.c 
    
#define _XOPEN_SOURCE /* glibc2 needs this */
#include <time.h>

#include <sys/time.h>
#include <stdio.h>

int main(int argc, char **  argv)
{


        struct tm process_starttime;
        struct timeval rightnow;

        if(argc < 2)
        {
                fprintf(stderr, "No args.");
                return 1;
        }
        strptime(argv[1], "%c", &process_starttime);
        time_t startTime = mktime(&process_starttime);

        gettimeofday(&rightnow, NULL);
        time_t timeNow = rightnow.tv_sec;

        //fprintf(stderr, "<%s>\n", argv[1]);
        //fprintf(stderr, "startTime: %ld\n", startTime);
        //fprintf(stderr, "now: %ld\n", timeNow);

        printf("%d", timeNow - startTime); 
        return 0;
}


You compile it to get the binary ./a.out:
$ gcc -c timediff.c 

and you run it (I'm looking for the process named loginwindow)

$ ps -xo lstart,pid,command | grep loginwindow | grep -v grep | awk -F '   ' '{printf("%s", $1)}' | xargs -0 ./a.out

6948$ 

6948 seconds. That's a bit off because of a timezone issue I imagine but I'm sure you can figure that out.

--CristianDraghici

