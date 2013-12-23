---
layout: page
title: BoostLibraries
---

**Q:** I've been trying to install the Boost [http://www.boost.org] C++ Libraries for a while now, and I haven't gotten it to work yet. I installed a Library using "make" the other day and it worked fine, but boost uses bjam to install itself. Also according to the Boost wiki  "only a few libraries require a library compilation step using Boost.Jam. These are: thread, regex and python. Graph also requires this step only if the graphviz feature is to be used. The other Boost libraries are contained completely within header files." I have no idea what that means, and if I don't have to compile it where should I put it? The website is pretty vague about all of this and I was wondering if anyone could offer help?

----

Can't help with that, but apparently it doesn't like installing on MacOSX due to a bug with gcc whereby files passed with -isystem are extern "C"'ed... which breaks the templates in, e.g., Spirit. (Spirit gave me 1286 errors, most of which were "template with C linkage" or some such.) Apparently you have to pass -I (that's a capital i, not an L or something like that) when including boost's headers, and I for one don't know how to do that with Xcode or my life would be a lot easier.

As it is, I'm using flex, and I'm not fond of it.

-- RobRix

----

For the portions of boost that are contained in the headers only. All you need to do for Xcode is add the boost directory to your **Header Search Paths** in your build settings to start using it. This would be the same as adding a     -I/Users/someuser/somedir/boost-1.30.2/ for a gcc command line build.

