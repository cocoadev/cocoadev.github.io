---
layout: page
title: InterCal
---

InterCal is a program for converting calendar dates from one system to another. Works far in the past and future. Supported calendars are: Gregorian, Julian, Chinese, Mayan, Jewish, Islamic, and French revolutionary.

Holidays/holydays (such as Easter and Yom Kippur) are highlighted.

----

INTERCAL is also, unfortunately, a programming language. Sort of. It's not really a programming language in the sense of a language that one writes programs with. I'm sure a program must have been written in INTERCAL at some point in the past, INTERCAL having been invented more than thirty years ago. But it was almost certainly a horrible program, user-hostile and chock full of bugs.

What follows is a sample program written in INTERCAL.
    
        DO (5) NEXT
    (5) DO FORGET #1
        PLEASE WRITE IN :1
        DO .1 <- '?":1~'#32768$#0'"$#1'~#3
        DO (1) NEXT
        DO :1 <- "'?":1~'#65535$#0'"$#65535'
                ~'#0$#65535'"$"'?":1~'#0$#65535'"
                $#65535'~'#0$#65535'"
        DO :2 <- #1
        PLEASE DO (4) NEXT
    (4) DO FORGET #1
        DO .1 <- "?':1~:2'$#1"~#3
        DO :1 <- "'?":1~'#65535$#0'"$":2~'#65535
                $#0'"'~'#0$#65535'"$"'?":1~'#0
                $#65535'"$":2~'#0$#65535'"'~'#0$#65535'"
        DO (1) NEXT
        DO :2 <- ":2~'#0$#65535'"
                $"'":2~'#65535$#0'"$#0'~'#32767$#1'"
        DO (4) NEXT
    (2) DO RESUME .1
    (1) PLEASE DO (2) NEXT
        PLEASE FORGET #1
        DO READ OUT :1
        PLEASE DO .1 <- '?"':1~:1'~#1"$#1'~#3
        DO (3) NEXT
        PLEASE DO (5) NEXT
    (3) DO (2) NEXT
        PLEASE GIVE UP


Determining what this program does, exactly, is left as an exercise for the reader.

Heh.  This kind of stuff was the first thing I thought of when I saw InterCal on the RecentChanges list. ++MarkDalrymple

----

Don't sprain anything with this exercise. 
By the way, we couldn't possibly leave without a discussion of the riveting <code>COME FROM</code> construct! 
Take a look at http://c2.com/cgi/wiki?ComeFrom

