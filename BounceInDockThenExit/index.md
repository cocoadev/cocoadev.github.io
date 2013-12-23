---
layout: page
title: BounceInDockThenExit
---

I'm creating an application that uses an external library. I've added the library to the project and compiled, everything run fine here, but on any other computer it fails. It just bounces a few times in the dock and then retires with not a single squeek.

{FrodeDanielsen}

----

dyld probably can't find the library. Check your Console/System Log.

-- FinlayDobbie

----

Ok. True. It says:
dyld: /Users/frodedan/Arbeid/Programmering/Cocoa/iD3/build/iD3.app/Contents/MacOS/iD3 can't open library: /usr/local/lib/libid3-3.8.0.dylib  (No such file or directory, errno = 2)

That file is really just a link (alias) to /usr/local/lib/libid3-3.8.0.0.0.dylib, which I have set to be copied into the bundle. I've verified that it exists. This is a dependency file for /usr/local/lib/libid3.a apparently, which also exists in the bundle.

{FrodeDanielsen}

