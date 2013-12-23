---
layout: page
title: HowToSetACustomIconWithRez
---

I've been wondering about this for a while, and I finally got something to work.  

To set an icns file *<source.icns>* to be the custom icon of a file *<target_file>*, do this:

    
echo "read 'icns' (-16455) \"*<source.icns>*\";" | /Developer/Tools/Rez -o *<target_file>*
/Developer/Tools/SetFile -a "C" *<target_file>*


To set the custom icon on a folder *<target_folder>*, do

    
echo "read 'icns' (-16455) \"*<source.icns>*\";" | /Developer/Tools/Rez -o `printf "*<target_folder>*/Icon\r"`
/Developer/Tools/SetFile -a "C" *<target_folder>*


Hope this helps somebody.

-KenFerry

