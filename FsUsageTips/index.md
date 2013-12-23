---
layout: page
title: FsUsageTips
---

 


This is to be a collection of tips on using the fabulously useful, but not very well known command line tool: fs_usage .
fs_usage gives you a real-time list of filesystem and network calls that all the processes on the system are doing, as they do them.
It's kind of like drinking from a firehose.

This command outputs the full output into a file:

sudo fs_usage -w > ~/foo

Once you have put the output of this into a file(~/foo in the example, change to suit yourself), you can use the following bits:

*brings up the one-line summaries of each of the system calls that are made.  This tip will do this for you.

whatis $(cat ~/foo | cut -f 3 -d " " | sort | uniq | sed -e 's/.*/&(2)/')

whatis brings up the one-line'ers; however, it needs "(2)" added to the end or it will find lots of useless stuff too.  cut -f 3 -d " " pull out the system calls from the fs_usage output; sort and uniq do what you would think.  the sed command adds the "(2)" bit.

*prints out a count of the syscalls used by each process that used them...

cat ~/foo | cut -c 13-30,152- | sort | uniq -c


Parts of the output (with character postitions, useful for cut)

*Full timestamp: -12
*Hour: -2
*Hour+Minute: -5
*Hour+Minute+Second: -8
*System call: 13-30
*Time Interval (How long the syscall took): 141-149
*Scheduled Out flag(a "W"): 151
*Process: 152-
*1st argument name(Can be A, D, or F): 31
*A or D argument(Starts with "A=" or "D="): 31-43  
*F argument: 31-34
*File paths(may be chopped off at the beginning): 48-140
*And more I haven't had a chance to investigate yet...


Some good resources on fs_usage are: 

*http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/fs_usage.1.html
*http://rentzsch.com/macosx/fs_usageIntro
*http://www.macworld.com/weblogs/macosxhints/2005/08/filesystemusage/index.php?lsrc=mwrss

