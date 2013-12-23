---
layout: page
title: NetworkTime
---



I am attempting to synchronize to a network time server from a cocoa application, but have not been able to.
I've tried to use ntpd with a configuration file that has only the time server I want to synch with. 
Is there any way to handle a time change without needing the user to authenticate?

Or even better, is there a way to start an NSTask that runs in an environment that has its time synchronized with my other time server?

I'm working with some time sensitive algorithms and unfortunately the server that the algorithm gets checked against is a few minutes slow.

----
I would suggest that you implement NTP in your own app, then use the results to compute a delta with the system clock that you can use to get crappy-server time.

----
The task that I'm trying to use is kpasswd, is there  a way to make this task think that it's being run at a different time?

----
At this point in time I'm thinking of implementing kpasswd and sntp in my application. It makes the application grow from 3 lines of code to several hundred, so I can't help but feel that I'm going down the wrong path. 
In the mean time I ordered Advance Cocoa Programming to see if there's any way that I could run an NSTask such that it believes that it is in a different time.

----

If anyone is following this thread hoping for a suggestion I think that I may have found one. I saw a shell script for setting up NTP for OS X, I may just use NSTask to run the commands in the shell script, or possibly just run the shell script.
    
#!/bin/sh
#
# script to enable time syncing on Mac OS X.

if [ `uname -s` != "Darwin" ]; then
  echo "error: script must only be run on Darwin systems"
  exit 1
fi

# set this to the NTP server hostname
TIMESERVER=time

# ntp.conf data
TIMECONFIG="$TIMESERVER minpoll 12 maxpoll 17"

# set proper values in NetInfo for ntpd (Mac OS X 10.0 and 10.1?)
niutil -create / /config/ntp
# $TIMECONFIG will look like:    pool.ntp.org minpoll 12 maxpoll 17
niutil -createprop / /config/ntp server $TIMECONFIG
niutil -resync /

# and set file data, just in case (Mac OS X 10.2?)
echo "server $TIMECONFIG" > /etc/ntp.conf

# enable timesyncing in startup preferences file
if grep ^TIMESYNC /etc/hostconfig >/dev/null; then
	perl -i -ple 's/-NO-/-YES-/ if /^TIMESYNC/' /etc/hostconfig
else
	echo "TIMESYNC=-YES-" >> /etc/hostconfig
fi

