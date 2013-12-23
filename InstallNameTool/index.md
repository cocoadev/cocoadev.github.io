---
layout: page
title: InstallNameTool
---

Hello,
I'm interested in using install_name_tool command to set a dylib's install path to a directory within the users home directory,
e.g. 
install_name_tool -id ~/Library/MyApp/libMyApp.dylib libMyApp.dylib

The problem is it automatically replaces the ~ with my home directory.  Is there a way so it can always pick the current HOME directory for this path? (like @executable_path for finding the file inside an application)

----

~some_users_name/Library/MyApp/libMyApp.dylib

Although, Might I suggest: ~some_users_name/Library/Application Support/Your Company Name/MyApp/libMyApp.dylib

Not knowing the context in which you are having difficulty, I will note that there is also the $HOME environment variable.

----

Expansion of variables (like $HOME) and ~ is done by the shell before the command you're running ever sees them. If you want to insert a literal ~ or a literal $HOME into the path, put '...' around it. The linker may not have any idea what to do with those characters once you get them in the path, but it doesn't hurt to try.

----

I tried your idea of putting '...' around the ~ and also on $HOME.  It couldn't load the library so I assume that the linker doesn't understand ~ or $HOME.  Does anybody have any other ideas? It seems like there should be something like @executable_path that points to the home directory

Maybe I should change where I install the dylib to. Is it safe to put it somewhere like /usr/local/MyApp ?

