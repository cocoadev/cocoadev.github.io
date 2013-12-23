---
layout: page
title: ObjectiveCeeFormattingTool
---

Does anyone have an indent.pro file that works with Objective-C code? Or know of another tool with similar functionality? I'm using the one below (which I got from [http://mail.gnu.org/archive/html/discuss-gnustep/2003-04/msg00085.html]) but it doesn't work very well, for the reason discussed in the thread at that link. (It doesn't deal too well with Objective-C @... directives. The suggested fix doesn't help.)

**indent.pro**

    -bad -bap -c33 -ci1 -d1 -di20 -i4 -l78 -nbc -nfc1 -psl -hnl -nsob -Tid -TBOOL -TSEL -TNS...

Wasn't there an old unix tool called astyle for artistic style or something? Might be worth investigating. -- MikeAmy 

I had & tested the JEdit plugin and it looks like it does the trick - thanx! Downloading the standalone tool now...

