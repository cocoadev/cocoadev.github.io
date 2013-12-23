---
layout: page
title: EasyLineCountInXCode
---

is there an easy way to get a complete line count of all the code you have written on a project in XCode? I really like to see how much I have written as time goes by, sometimes it amazes me.

Thanks,

-Peter

It doesn't involve XCode, but what the heck:

    
cd your/project/directory
wc -l `find . -name **.h' -or -name **.m'`


Modify the find if you want to count other types of files, such as .mm files.


    
cd your/project/directory
wc -l *.[hm]

count lines in .h and .m files.

*It won't find files in subdirectories, though. I usually make quite a few of those to better organize my source files. That's why I use the find code above.*


This is Perfect - Thanks.

------
Note that if your filenames contain spaces or special characters, or you have several thousand files to work with, simply including your find command in backticks won't be enough.  xargs is your friend:

    
cd your/project/directory
find . -name **.h' -o -name **.m' -print0 | xargs -0 wc -l

