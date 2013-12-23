---
layout: page
title: HeadderFile
---

I have defined constants in a headder file & included in my prog. Now some situation I want to swap values in defined constants headder file while my main program doing initilization. It will help for my prog efficient. Pls tell me the way or alternative way....... 

----

If you defined these "constants" using the #define directive, their values are substituted for their names at compile time. It's too late to change them at run time. If you defined them as const variables, then their values are constant and they can't be changed. It sounds like the "constants" you're talking about really ought to be stored as variables so that you can change them later. It would help us help you if you'd explain a little more about your situation.

Also, there's just one 'd' in 'header'. -CS

